---
title: "Tuần 1: 17/04 - 24/04"
date: 2026-07-10
weight: 1
chapter: false
---

## Tuần 1: Kết nối thành viên & Tìm hiểu dịch vụ AWS cơ bản (EC2, CLI)

### Mục tiêu tuần 1:
* Kết nối, làm quen với các thành viên và mentors trong First Cloud Journey.
* Hiểu tổng quan về các dịch vụ AWS cốt lõi, nắm vững cách sử dụng AWS Console và AWS Command Line Interface (CLI) để quản trị tài nguyên thông qua dòng lệnh.
* Thực hành tạo, kết nối và quản trị vòng đời máy chủ ảo Amazon Elastic Compute Cloud (EC2).

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------------ | --------------------------------------------------------------------------------- |
| 2 | - Tham gia buổi họp định hướng định kỳ, làm quen với các thành viên nhóm và mentors của chương trình FCJ.<br>- Đọc kỹ và ghi nhớ các nội quy, quy chế làm việc trực tuyến và trực tiếp tại đơn vị thực tập. | 17/04/2026 | 17/04/2026 | Sổ tay thực tập |
| 3 | - Tìm hiểu tổng quan về điện toán đám mây và AWS.<br>- Nghiên cứu các nhóm dịch vụ nền tảng:<br>&emsp; + Compute (EC2, ECS, Lambda)<br>&emsp; + Storage (S3, EBS, EFS)<br>&emsp; + Networking (VPC, Route53)<br>&emsp; + Database (RDS, DynamoDB) | 18/04/2026 | 18/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 4 | - Đăng ký tài khoản AWS Free Tier để thực hành cá nhân.<br>- Tìm hiểu kiến thức về quản lý tài khoản và chi phí (AWS Billing & Cost Management).<br>- Cài đặt AWS CLI v2 trên hệ điều hành cá nhân.<br>- Cấu hình Access Key/Secret Key để xác thực kết nối. | 19/04/2026 | 19/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 5 | - Nghiên cứu sâu về máy chủ ảo Amazon EC2:<br>&emsp; + Các họ Instance Types phù hợp cho từng bài toán (T-family, C-family, M-family)<br>&emsp; + Tìm hiểu Amazon Machine Image (AMI)<br>&emsp; + Phân biệt ổ đĩa EBS (Elastic Block Store) và Instance Store.<br>- Nghiên cứu các phương pháp bảo mật kết nối SSH và quản lý địa chỉ IP tĩnh Elastic IP. | 20/04/2026 | 21/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 6 | - **Thực hành thực tế:**<br>&emsp; + Khởi tạo một máy chủ ảo EC2 chạy hệ điều hành Ubuntu Server thông qua AWS Console.<br>&emsp; + Cấu hình Security Group cho phép cổng 22 (SSH) và cổng 80 (HTTP).<br>&emsp; + Tạo Key Pair mới và thực hiện kết nối SSH từ xa bằng Terminal.<br>&emsp; + Gắn thêm một ổ đĩa EBS ngoài và thực hiện phân vùng ổ đĩa trên Linux. | 21/04/2026 | 21/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |

### Kết quả đạt được tuần 1:
*   **Về kiến thức:** Hiểu rõ bản chất của điện toán đám mây (Cloud Computing) và vai trò của các dịch vụ Managed Services trên AWS giúp tăng tốc độ triển khai ứng dụng như thế nào. Nắm chắc khái niệm ảo hóa tài nguyên tính toán và lưu trữ.
*   **Về thực hành:**
    *   Tạo thành công tài khoản AWS Free Tier và thiết lập cảnh báo ngân sách (AWS Budget) tránh phát sinh chi phí vượt mức.
    *   Cài đặt thành công AWS CLI v2 và cấu hình thông tin định danh thông qua lệnh `aws configure`.
    *   Khởi tạo và kiểm soát vòng đời máy chủ EC2 (Start, Stop, Terminate) bằng cả giao diện Console và câu lệnh CLI (ví dụ câu lệnh: `aws ec2 run-instances`).
    *   Remote SSH vào máy chủ Linux thành công bằng cách sử dụng tệp chìa khóa bí mật `.pem` và phân quyền truy cập an toàn (`chmod 400`).
    *   Hiểu và thực hành gắn thêm đĩa cứng EBS, định dạng hệ thống tệp tin Ext4 và mount đĩa cứng vào thư mục hệ điều hành Linux.
