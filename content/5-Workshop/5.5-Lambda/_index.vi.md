---
title: "5.5 Deploy Backend"
date: 2026-07-10
weight: 5
chapter: false
---

# 5.5 Biên dịch & Deploy C# Backend lên AWS Lambda Custom Runtime

Hệ thống tính toán không máy chủ (Serverless Compute) AWS Lambda yêu cầu mã nguồn phải được tối ưu hóa cao để giảm thiểu tối đa thời gian khởi động nguội (Cold Start latency) và tiết kiệm tài nguyên. Do AWS Lambda chưa tích hợp sẵn runtime quản trị chính thức cho phiên bản .NET 10 mới nhất, chúng ta sẽ cấu hình triển khai ứng dụng C# Web API dưới dạng **Custom Runtime** (Sử dụng hệ điều hành tối giản **Amazon Linux 2023**).

---

### 1. Quy trình biên dịch tự đóng gói (Self-contained Publishing)

Vì môi trường chạy AWS Lambda chạy trên hệ điều hành Linux 64-bit, việc biên dịch ứng dụng C# trên máy tính cá nhân (thường chạy Windows hoặc macOS) đòi hỏi kỹ thuật biên dịch chéo nền tảng (Cross-compilation). 

Chúng ta cần xuất ứng dụng ở chế độ **Self-contained** (tự nhúng sẵn runtime .NET tối giản vào bên trong tệp nhị phân để Lambda chạy trực tiếp mà không cần hệ điều hành cài sẵn .NET SDK) và đổi tên tệp thực thi chính thành `bootstrap`.

Mở terminal tại thư mục chứa mã nguồn Backend `backend/` và thực thi các câu lệnh PowerShell sau:

```powershell
# Bước 1: Xóa sạch các thư mục build cũ để tránh xung đột file cấu hình
Remove-Item -Path "publish" -Recurse -Force -ErrorAction SilentlyContinue

# Bước 2: Thực hiện biên dịch tối ưu hóa Release nhắm mục tiêu chạy trực tiếp trên Linux x64
# Tham số --self-contained true giúp đóng gói toàn bộ các thư viện .NET cần thiết vào thư mục output
dotnet publish -c Release -r linux-x64 --self-contained true -o publish /p:GenerateRuntimeConfigurationFiles=true

# Bước 3: Đổi tên file nhị phân thực thi chính từ "SmartDorm.Api" thành "bootstrap"
# > [!IMPORTANT]
# > Đây là quy chuẩn bắt buộc của Custom Runtime trên AWS Lambda. Khi khởi dựng container, 
# > AWS Lambda sẽ tìm kiếm đúng một file thực thi có tên là "bootstrap" ở thư mục gốc để kích hoạt API.
Rename-Item -Path "publish/SmartDorm.Api" -NewName "bootstrap"

# Bước 4: Nén toàn bộ tệp tin bên trong thư mục publish thành file backend.zip
# Tệp backend.zip này sẽ được Terraform tải lên đám mây làm mã nguồn cho Lambda
Compress-Archive -Path "publish/*" -DestinationPath "../backend.zip" -Force
```

---

### 2. Định nghĩa Hàm Lambda Serverless trong Terraform (`main.tf`)

Trong tệp cấu hình Terraform, chúng ta khai báo tài nguyên Hàm Lambda và thiết lập các biến môi trường để cấu hình chuỗi kết nối động trỏ tới máy chủ cơ sở dữ liệu RDS PostgreSQL vừa khởi tạo:

```hcl
# 1. KHỞI TẠO S3 BUCKET TẠM THỜI ĐỂ LƯU FILE ZIP MÃ NGUỒN
resource "aws_s3_bucket" "lambda_deploy_bucket" {
  bucket_prefix = "smartdorm-lambda-deploy-"
  force_destroy = true # Tự động xóa bucket khi dọn dẹp hạ tầng
}

resource "aws_s3_object" "lambda_code" {
  bucket      = aws_s3_bucket.lambda_deploy_bucket.id
  key         = "backend.zip"
  source      = "../backend.zip"
  source_hash = filebase64sha256("../backend.zip") # Kiểm soát hash để tự động deploy lại khi code backend thay đổi
}

# 2. ĐỊNH NGHĨA HÀM AWS LAMBDA CHÍNH
resource "aws_lambda_function" "backend_api" {
  function_name    = "SmartDormBackendAPI"
  role             = aws_iam_role.lambda_exec_role.arn
  handler          = "bootstrap"            # Tên file chạy thực thi
  runtime          = "provided.al2023"      # Hệ điều hành Custom Runtime Amazon Linux 2023
  s3_bucket        = aws_s3_bucket.lambda_deploy_bucket.id
  s3_key           = aws_s3_object.lambda_code.key
  source_code_hash = filebase64sha256("../backend.zip")

  timeout     = 30  # Thời gian tối đa xử lý request (30 giây)
  memory_size = 512 # Cấp phát RAM 512MB (AWS sẽ tự cấp cấu hình CPU tương ứng, giúp giảm cold start đáng kể)

  environment {
    variables = {
      # Tự động ráp nối thông tin Endpoint cơ sở dữ liệu từ RDS Instance sang biến môi trường kết nối của EF Core
      ConnectionStrings__DefaultConnection = "Host=${aws_db_instance.smartdorm_db.address};Database=smartdorm;Username=${aws_db_instance.smartdorm_db.username};Password=${var.db_password};Port=5432"
      Jwt__Secret                          = var.jwt_secret
      AWS__Region                          = var.aws_region
      AWS__BucketName                      = aws_s3_bucket.frontend_bucket.id
      DOTNET_SYSTEM_GLOBALIZATION_INVARIANT = "true" # Tối giản dung lượng nhị phân, loại bỏ thư viện văn hóa vùng miền
    }
  }
}
```

---

### 3. Mở cổng kết nối HTTPS cho Backend API (Expose Endpoint)

Để Next.js Frontend chạy trên trình duyệt người dùng có thể thực hiện các cuộc gọi API lấy dữ liệu, chúng ta cần mở cổng kết nối HTTPS công khai cho AWS Lambda. Có hai phương pháp giải quyết tối ưu trong dự án:

#### Phương pháp 1: Sử dụng AWS Lambda Function URL (Tối ưu chi phí nhất - Free)
*   **Mô tả:** Đây là tính năng mới của AWS, cung cấp trực tiếp một địa chỉ URL HTTPS bảo mật trỏ thẳng vào Lambda mà không thông qua bất kỳ thành phần trung gian nào. Không phát sinh bất kỳ chi phí duy trì cố định nào.
*   **Cấu hình CORS:** Cho phép chấp nhận các yêu cầu CORS từ bất kỳ nguồn giao diện (Frontend) nào để phục vụ quá trình debug và kiểm thử diễn ra trơn tru.

```hcl
resource "aws_lambda_function_url" "api_endpoint" {
  function_name      = aws_lambda_function.backend_api.function_name
  authorization_type = "NONE" # Cho phép truy cập công khai không qua IAM Auth

  cors {
    allow_credentials = false
    allow_origins     = ["*"] # Chấp nhận request từ mọi nguồn frontend
    allow_methods     = ["*"]
    allow_headers     = ["date", "keep-alive", "content-type", "authorization"]
    max_age           = 86400
  }
}
```

#### Phương pháp 2: Sử dụng Amazon API Gateway (Tích hợp sâu rộng)
*   **Mô tả:** Trong trường hợp nhà mạng hoặc doanh nghiệp áp đặt các chính sách chặn bảo mật đối với Function URL công khai, chúng ta sử dụng dịch vụ HTTP API Gateway để tạo một cổng API Proxy bảo mật làm nhiệm vụ trung chuyển và định tuyến toàn bộ các luồng request công khai vào Lambda.
