---
title: "5.6 Cấu hình S3"
date: 2026-07-10
weight: 6
chapter: false
---

# 5.6 Cấu hình Amazon S3 & Tích hợp Upload File ở Backend

Khi chạy ứng dụng trên máy tính cá nhân (môi trường Local Development), các tệp tin tải lên (ảnh đại diện sinh viên, ảnh hóa đơn, ảnh CCCD) thường được ghi thẳng trực tiếp lên đĩa cứng vật lý thông qua đường dẫn cục bộ (như thư mục `wwwroot/uploads`). 

Tuy nhiên, khi triển khai trên mô hình Serverless đám mây AWS Lambda, cơ chế này lập tức phát sinh lỗi nghiêm trọng:
*   **Hệ thống tệp chỉ đọc (Read-only filesystem):** Để đảm bảo tính an toàn bảo mật tuyệt đối, AWS thiết lập toàn bộ vùng đĩa cứng chạy của container Lambda ở chế độ chỉ đọc.
*   **Thư mục tạm `/tmp` tạm bợ:** Mặc dù Lambda cung cấp một thư mục ghi tạm thời là `/tmp` với dung lượng nhỏ (tối đa 512MB đến 10GB), thư mục này là **ephemeral (tạm thời)**. Dữ liệu bên trong sẽ bị xóa sạch hoàn toàn ngay khi container Lambda tắt đi (Scale-down về 0) hoặc khởi động lại container mới.

Để giải quyết triệt để vấn đề này, dự án SmartDorm tích hợp dịch vụ lưu trữ đám mây **Amazon S3 (Simple Storage Service)** làm nơi lưu trữ tài nguyên tĩnh vĩnh viễn với độ bền dữ liệu lên tới 99.999999999% (11 con số 9).

---

### 1. Cấp quyền cho Lambda qua IAM Policy (`main.tf`)

AWS tuân thủ nguyên lý bảo mật quyền tối thiểu (Least Privilege). Theo mặc định, Hàm Lambda hoàn toàn không có quyền ghi hay đọc dữ liệu từ bất kỳ dịch vụ nào khác của AWS, bao gồm cả S3 Bucket. Nếu cố tình ghi dữ liệu mà không cấp quyền, SDK sẽ ném ra ngoại lệ `AmazonS3Exception: Access Denied`.

Ta bổ sung cấu hình quyền hạn cho IAM Role của Lambda trong tệp Terraform để cấp phép cho Lambda thực thi các hành động tải ảnh lên S3:

```hcl
resource "aws_iam_role_policy" "lambda_s3_policy" {
  name = "smartdorm_lambda_s3_policy"
  role = aws_iam_role.lambda_exec_role.id # Gắn trực tiếp chính sách này vào Role của Lambda

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:PutObject",     # Quyền ghi đè/tải lên tệp mới
          "s3:PutObjectAcl",  # Quyền thiết lập nhãn phân quyền công khai cho tệp tải lên
          "s3:GetObject"      # Quyền đọc tệp tin
        ]
        Resource = [
          # Giới hạn an toàn: Chỉ cho phép Lambda thực thi thao tác ghi đè trên duy nhất S3 Bucket của SmartDorm
          "${aws_s3_bucket.frontend_bucket.arn}/*"
        ]
      }
    ]
  })
}
```

---

### 2. Viết mã nguồn xử lý Upload File ở Backend (`UploadController.cs`)

Lập trình viên xây dựng một logic linh hoạt tại [UploadController.cs](file:///m:/SmartDorm/backend/Controllers/UploadController.cs). Lớp Controller này tự động nhận diện môi trường chạy để đưa ra quyết định:
1.  **Chạy trên Cloud AWS (Production):** Khi phát hiện sự tồn tại của biến môi trường `AWS__BucketName`, hệ thống sẽ kích hoạt **Amazon S3 Client SDK**, chuyển tiếp luồng dữ liệu tệp tin (Binary Stream) lên đám mây Amazon S3 công khai và trả về đường dẫn URL HTTPS tĩnh dạng `https://[bucket-name].s3.amazonaws.com/uploads/...`.
2.  **Chạy trên máy tính cá nhân (Local Development):** Khi biến môi trường trống, hệ thống tự động kích hoạt cơ chế dự phòng cục bộ (Local Fallback), tạo thư mục và lưu file trực tiếp vào thư mục tĩnh `wwwroot/uploads` để hỗ trợ lập trình viên test nhanh ngoại tuyến.

Dưới đây là chi tiết mã nguồn C# thực hiện logic điều hướng thông minh này:

```csharp
using Amazon.S3;
using Amazon.S3.Model;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System;
using System.IO;
using System.Threading.Tasks;

namespace SmartDorm.Api.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class UploadController : ControllerBase
    {
        [HttpPost]
        [DisableRequestSizeLimit] // Vô hiệu hóa giới hạn dung lượng request mặc định của ASP.NET Core để tải file lớn
        public async Task<IActionResult> UploadFile(IFormFile file)
        {
            if (file == null || file.Length == 0)
                return BadRequest(new { success = false, message = "Không tìm thấy dữ liệu tệp tin tải lên" });

            // Đọc tên S3 bucket từ biến môi trường của AWS Lambda được cấu hình qua Terraform
            var bucketName = Environment.GetEnvironmentVariable("AWS__BucketName");

            if (!string.IsNullOrEmpty(bucketName))
            {
                try
                {
                    // Khởi tạo S3 Client sử dụng thông tin IAM Role đã cấp cho Lambda
                    var s3Client = new AmazonS3Client();
                    
                    // Tạo tên file độc nhất để tránh bị ghi đè dữ liệu cũ
                    var fileKey = $"uploads/{Guid.NewGuid()}_{file.FileName}";

                    using (var stream = file.OpenReadStream())
                    {
                        var putRequest = new PutObjectRequest
                        {
                            BucketName = bucketName,
                            Key = fileKey,
                            InputStream = stream,
                            ContentType = file.ContentType
                        };
                        
                        await s3Client.PutObjectAsync(putRequest);
                    }

                    // Tạo đường dẫn HTTPS công khai để người dùng truy cập trực tiếp hình ảnh
                    var publicUrl = $"https://{bucketName}.s3.amazonaws.com/{fileKey}";
                    return Ok(new { success = true, url = publicUrl });
                }
                catch (Exception ex)
                {
                    // Ghi nhận nhật ký lỗi hệ thống
                    return StatusCode(500, new { success = false, message = "Lỗi khi upload tệp tin lên AWS S3 Cloud", error = ex.Message });
                }
            }

            // CƠ CHẾ DỰ PHÒNG CỤC BỘ (LOCAL FALLBACK) KHI LẬP TRÌNH LOCAL
            var uploadDir = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/uploads");
            
            // Tự động khởi tạo thư mục uploads nếu chưa tồn tại trên ổ cứng local
            if (!Directory.Exists(uploadDir)) 
                Directory.CreateDirectory(uploadDir);

            var fileName = $"{Guid.NewGuid()}_{file.FileName}";
            var filePath = Path.Combine(uploadDir, fileName);

            using (var stream = new FileStream(filePath, FileMode.Create))
            {
                await file.CopyToAsync(stream);
            }

            var localUrl = $"/uploads/{fileName}";
            return Ok(new { success = true, url = localUrl });
        }
    }
}
```
