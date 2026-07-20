---
title: "5.2 Yêu cầu chuẩn bị"
date: 2026-07-10
weight: 2
chapter: false
---

# 5.2 Yêu cầu chuẩn bị công cụ & Cấu hình môi trường

Để đảm bảo quá trình thực hành diễn ra trơn tru và tránh các lỗi phát sinh do sai lệch phiên bản, bạn cần tiến hành cài đặt đầy đủ các công cụ lập trình dưới đây và cấu hình quyền truy cập AWS.

---

### 1. Khởi tạo tài khoản AWS & IAM User

Hạ tầng đám mây yêu cầu bạn phải có tài khoản AWS đang hoạt động.
*   **Đăng ký tài khoản:** Truy cập [AWS Portal](https://aws.amazon.com/) để đăng ký. AWS yêu cầu nhập thẻ tín dụng/ghi nợ (Visa/Mastercard) để xác minh danh tính. Sau khi xác minh, tài khoản của bạn sẽ có hiệu lực sử dụng ngay lập tức trong phạm vi gói **Free Tier**.
*   **Tạo IAM User quản trị (Khuyến nghị bảo mật):**
    > [!WARNING]
    > **Tuyệt đối không sử dụng tài khoản Root** để phát triển ứng dụng hàng ngày nhằm tránh nguy cơ rò rỉ khóa bảo mật làm lộ thông tin tài khoản và phát sinh chi phí khổng lồ.
    
    1. Truy cập vào AWS Console, tìm kiếm dịch vụ **IAM (Identity and Access Management)**.
    2. Chọn **Users** -> **Create user**. Đặt tên người dùng (ví dụ: `SmartDormDev`).
    3. Ở bước phân quyền, chọn **Attach policies directly** và tích chọn quyền **`AdministratorAccess`**.
    4. Sau khi tạo xong User, chọn tab **Security credentials** -> Cuộn xuống mục **Access keys** -> Nhấp **Create access key**.
    5. Chọn mục đích sử dụng là **Command Line Interface (CLI)** và tải xuống tệp chứa hai khóa quan trọng: **`Access Key ID`** và **`Secret Access Key`**. Lưu trữ tệp này ở nơi an toàn.

---

### 2. Cài đặt các công cụ dòng lệnh (CLI Tools)

#### A. AWS CLI v2 (Quản trị AWS qua Terminal)
*   **Hệ điều hành Windows:**
    Tải bản cài đặt MSI trực tiếp tại: [AWS CLI MSI Installer](https://awscli.amazonaws.com/AWSCLIV2.msi) hoặc cài qua dòng lệnh PowerShell (Winget):
    ```powershell
    winget install Amazon.AwsCLI
    ```
*   **Cấu hình xác thực định danh:**
    Mở terminal và gõ lệnh sau:
    ```bash
    aws configure
    ```
    Hệ thống sẽ hỏi và bạn cần nhập chính xác:
    ```text
    AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE (Nhập từ file tải ở bước 1)
    AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    Default region name [None]: ap-southeast-1 (Khu vực Singapore gần Việt Nam nhất)
    Default output format [None]: json
    ```
    *Kiểm tra kết nối:* Thực thi lệnh `aws sts get-caller-identity`. Nếu màn hình trả về định dạng JSON chứa ID tài khoản và ARN của User thì việc cấu hình đã hoàn tất thành công.

#### B. Terraform CLI (Quản lý hạ tầng IaC)
*   **Cài đặt nhanh qua Winget (Windows):**
    ```powershell
    winget install Hashicorp.Terraform
    ```
*   **Cài đặt thủ công:**
    1. Tải file nén tại [Terraform Downloads](https://developer.hashicorp.com/terraform/downloads).
    2. Giải nén được duy nhất file `terraform.exe`.
    3. Di chuyển file này vào một thư mục cố định (ví dụ: `C:\tools\terraform\`).
    4. Thêm đường dẫn `C:\tools\terraform\` vào biến môi trường **PATH** của hệ thống Windows để có thể gọi lệnh `terraform` từ bất kỳ đâu.
*   *Kiểm tra:* Chạy lệnh `terraform -version` để đảm bảo hệ thống nhận diện được Terraform CLI.

---

### 3. Cài đặt SDK và môi trường chạy mã nguồn (SDKs & Runtimes)

#### A. .NET SDK 10.0 (Biên dịch Backend C#)
*   Dự án SmartDorm sử dụng phiên bản C# mới nhất trên .NET 10.0 để tận dụng các cải tiến về hiệu năng khởi động lạnh (Cold start) trên Serverless.
*   Tải trực tiếp bộ cài đặt SDK tại: [Download .NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0). Hãy chọn đúng phiên bản kiến trúc CPU của bạn (x64 hoặc ARM64).
*   *Kiểm tra:* Thực thi lệnh `dotnet --list-sdks` trong terminal, đảm bảo dòng `.NET SDK 10.x` được hiển thị.

#### B. Node.js LTS (Biên dịch Frontend Next.js)
*   Next.js yêu cầu môi trường chạy NodeJS. Bạn nên tải bản cài đặt **LTS (Long Term Support)** để đảm bảo sự ổn định.
*   Tải tại: [NodeJS Official Site](https://nodejs.org/).
*   *Kiểm tra:* Chạy lệnh `node -v` và `npm -v` để kiểm tra.

#### C. Docker Desktop (Giả lập PostgreSQL Database)
*   Để chạy thử nghiệm database PostgreSQL local một cách nhanh gọn mà không cần cài đặt trực tiếp hệ quản trị cơ sở dữ liệu lên máy vật lý, chúng ta sử dụng Docker container.
*   Tải bộ cài đặt tại: [Docker Desktop](https://www.docker.com/products/docker-desktop). Sau khi cài đặt thành công, hãy đảm bảo Docker Service đang chạy ở chế độ nền (Background).
