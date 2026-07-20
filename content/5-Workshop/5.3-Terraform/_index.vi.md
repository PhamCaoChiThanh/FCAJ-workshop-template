---
title: "5.3 Khởi tạo Terraform"
date: 2026-07-10
weight: 3
chapter: false
---

# 5.3 Triển khai hạ tầng đám mây tự động bằng Terraform IaC

Phương pháp khởi tạo tài nguyên thủ công bằng cách nhấp chuột trên giao diện AWS Console (ClickOps) thường rất dễ xảy ra lỗi cấu hình sai sót, khó quản lý lịch sử thay đổi và không thể nhân bản nhanh chóng hạ tầng. Do đó, dự án SmartDorm được định nghĩa toàn bộ tài nguyên hạ tầng bằng mã nguồn khai báo **Terraform (Infrastructure as Code - IaC)**.

---

### 1. Khai báo Nhà cung cấp dịch vụ AWS (`provider.tf`)

Mỗi khi chạy Terraform, nó cần liên kết với AWS API thông qua các module thư viện thích hợp. Tệp `provider.tf` quy định việc tải về phiên bản AWS Provider phù hợp và xác định khu vực địa lý áp dụng:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0" # Sử dụng phiên bản Provider AWS v5 ổn định
    }
  }
}

provider "aws" {
  region = var.aws_region # Định nghĩa vùng chạy dịch vụ (mặc định: Singapore)
}
```

---

### 2. Thiết kế hạ tầng mạng & RDS Database (`main.tf`)

Hạ tầng mạng đám mây của SmartDorm được phân vùng bảo mật nghiêm ngặt.
*   **VPC (Virtual Private Cloud):** Tạo ra một mạng ảo độc lập, cách ly hoàn toàn với các tài khoản khách hàng khác trên AWS.
*   **Subnet Phân vùng mạng:**
    *   *Subnet A (Public Subnet):* Chứa Internet Gateway để các tài nguyên bên trong dải IP này (như AWS Lambda) có thể kết nối ra ngoài Internet để gửi thư hoặc gọi Bedrock AI API.
    *   *Subnet B (Private Subnet):* Cách ly hoàn toàn để lưu trữ máy chủ cơ sở dữ liệu Amazon RDS PostgreSQL, chặn mọi truy cập trực tiếp từ ngoài internet để bảo vệ dữ liệu tối đa.
*   **RDS PostgreSQL:** Chọn dòng chip ARM `db.t4g.micro` giúp tối ưu hiệu năng và nằm gọn trong chương trình khuyến mãi Free Tier 750 giờ/tháng của AWS.

Dưới đây là nội dung chi tiết mã nguồn [main.tf](file:///m:/SmartDorm/terraform/main.tf):

```hcl
# 1. KHỞI TẠO MẠNG VPC ĐỘC LẬP (Dải IP 10.0.0.0/16 cung cấp hơn 65,000 địa chỉ IP ảo)
resource "aws_vpc" "smartdorm_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true # Bật tính năng cấp phát tên miền DNS cho các tài nguyên trong VPC
  enable_dns_support   = true
  tags = {
    Name = "smartdorm-vpc"
  }
}

# 2. INTERNET GATEWAY (IGW) - Cổng kết nối ra Internet công cộng
resource "aws_internet_gateway" "smartdorm_igw" {
  vpc_id = aws_vpc.smartdorm_vpc.id
  tags = {
    Name = "smartdorm-igw"
  }
}

# 3. PHÂN VÙNG SUBNETS CHI TIẾT
# Subnet Public A - Dùng cho các dịch vụ tính toán và API Gateway
resource "aws_subnet" "smartdorm_subnet_a" {
  vpc_id                  = aws_vpc.smartdorm_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "${var.aws_region}a" # Availability Zone ap-southeast-1a
  map_public_ip_on_launch = true                  # Tự cấp phát IP Public khi khởi chạy
  tags = {
    Name = "smartdorm-subnet-a"
  }
}

# Subnet Private B - Phân vùng cách ly, lưu trữ database nhạy cảm
resource "aws_subnet" "smartdorm_subnet_b" {
  vpc_id                  = aws_vpc.smartdorm_vpc.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "${var.aws_region}b" # Availability Zone ap-southeast-1b
  map_public_ip_on_launch = true                  # Bật IP Public để dev local cấu hình database dễ dàng
  tags = {
    Name = "smartdorm-subnet-b"
  }
}

# 4. THIẾT LẬP ROUTE TABLE & ĐỊNH TUYẾN MẠNG
resource "aws_route_table" "smartdorm_rt" {
  vpc_id = aws_vpc.smartdorm_vpc.id
  route {
    cidr_block = "0.0.0.0/0" # Định tuyến mọi traffic ra ngoài Internet
    gateway_id = aws_internet_gateway.smartdorm_igw.id
  }
  tags = {
    Name = "smartdorm-rt"
  }
}

resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.smartdorm_subnet_a.id
  route_table_id = aws_route_table.smartdorm_rt.id
}

resource "aws_route_table_association" "b" {
  subnet_id      = aws_subnet.smartdorm_subnet_b.id
  route_table_id = aws_route_table.smartdorm_rt.id
}

# 5. CẤU HÌNH NHÓM BẢO MẬT (SECURITY GROUP) CHO DATABASE
resource "aws_security_group" "rds_sg" {
  name        = "smartdorm_rds_sg"
  description = "Cho phep ket noi PostgreSQL vao cổng 5432"
  vpc_id      = aws_vpc.smartdorm_vpc.id

  # Cho phép các kết nối cổng 5432 từ internet để lập trình viên cấu hình nhanh database local
  ingress {
    description = "PostgreSQL"
    from_port   = 5432
    to_port     = 5432
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1" # Cho phép kết nối đi ra bất kỳ đâu
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# 6. KHỞI TẠO AWS RDS POSTGRESQL DATABASE
resource "aws_db_subnet_group" "smartdorm_db_subnet_group" {
  name       = "smartdorm-db-subnet-group"
  subnet_ids = [aws_subnet.smartdorm_subnet_a.id, aws_subnet.smartdorm_subnet_b.id]
  tags = {
    Name = "smartdorm-db-subnet-group"
  }
}

resource "aws_db_instance" "smartdorm_db" {
  identifier             = "smartdorm-postgres-db"
  allocated_storage      = 20
  storage_type           = "gp2"
  engine                 = "postgres"
  engine_version         = "15" # Sử dụng engine PostgreSQL bản 15
  instance_class         = "db.t4g.micro" # Dòng instance dùng chip Graviton ARM, miễn phí theo diện Free Tier
  username               = var.db_username
  password               = var.db_password
  parameter_group_name   = "default.postgres15"
  skip_final_snapshot    = true
  publicly_accessible    = true # Cho phép truy cập công khai để tiện thao tác chạy migration/dev local
  vpc_security_group_ids = [aws_security_group.rds_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.smartdorm_db_subnet_group.name
}
```

---

### 3. Tách biệt biến cấu hình bảo mật (`variables.tf`)

Để tránh việc vô tình đẩy mật khẩu database lên GitHub làm lộ thông tin hệ thống, ta tách các biến nhạy cảm ra một tệp riêng:

```hcl
variable "aws_region" {
  type    = string
  default = "ap-southeast-1"
}

variable "db_username" {
  type    = string
  default = "postgres"
}

variable "db_password" {
  type      = string
  sensitive = true # Đánh dấu sensitive để Terraform tự động che mật khẩu trên log xuất ra màn hình
}
```

---

### 4. Các bước triển khai hạ tầng chi tiết

Di chuyển terminal hoặc Command Prompt vào thư mục cấu hình hạ tầng `terraform/` và chạy lần lượt các câu lệnh sau:

*   **Bước 1: Khởi tạo thư mục làm việc và tải các thư viện Provider:**
    ```bash
    terraform init
    ```
    > [!NOTE]
    > Terraform sẽ tự động tải các plugin tương thích với hệ điều hành và lưu trữ trong thư mục ẩn `.terraform/`.

*   **Bước 2: Kiểm tra cấu hình và xem trước các thay đổi hạ tầng đám mây:**
    ```bash
    terraform plan -var="db_password=Chithanh123plus"
    ```
    *Kiểm tra danh sách tài nguyên chuẩn bị tạo, đảm bảo không có lỗi cú pháp hoặc xung đột hạ tầng cũ.*

*   **Bước 3: Thực thi tạo dựng hạ tầng đám mây AWS:**
    ```bash
    terraform apply -var="db_password=Chithanh123plus" -auto-approve
    ```
    > [!IMPORTANT]
    > Quá trình cấp phát tài nguyên mạng ảo và máy chủ vật lý ảo cho Amazon RDS PostgreSQL sẽ tốn từ **3 đến 5 phút**. Đừng tắt terminal đột ngột. Hãy đợi đến khi dòng chữ báo thành công `Apply complete!` được hiển thị và ghi lại địa chỉ **RDS Endpoint** được xuất ra ở phần Output.
