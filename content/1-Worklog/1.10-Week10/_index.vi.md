---
title: "Tuần 10: 28/06 - 05/07"
date: 2026-07-10
weight: 10
chapter: false
---

## Tuần 10: Viết Terraform IaC & Triển khai RDS PostgreSQL (28/06 - 05/07)

### Công việc đã làm
*   Viết kịch bản Hạ tầng dưới dạng mã (Infrastructure as Code - IaC) sử dụng **Terraform** để định nghĩa tài nguyên AWS.
*   Cấu hình VPC ảo, Internet Gateway, Subnet, Route Tables và Security Groups trên AWS.
*   Khởi tạo cơ sở dữ liệu quan hệ quản trị **Amazon RDS PostgreSQL** sử dụng cấu hình tối ưu chi phí: dòng instance `db.t4g.micro` chạy trên vi xử lý ARM Graviton2 để được miễn phí 750 giờ/tháng thuộc diện Free Tier.

### Chi tiết hạ tầng Terraform
Kịch bản khởi tạo:
*   Mạng VPC có địa chỉ CIDR `10.0.0.0/16`.
*   Cấu hình Security Group mở cổng `5432` từ xa cho phép Lambda gọi vào database, đồng thời chặn mọi truy cập không mong muốn từ internet để bảo mật database.

### Kết quả đạt được
*   Cơ sở hạ tầng mạng ảo và database PostgreSQL hoạt động ổn định trên đám mây AWS thông qua kịch bản tự động hóa bằng code.
