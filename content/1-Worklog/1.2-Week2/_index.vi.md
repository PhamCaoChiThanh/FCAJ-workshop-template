---
title: "Tuần 2: 24/04 - 02/05"
date: 2026-07-10
weight: 2
chapter: false
---

## Tuần 2: Nghiên cứu chuyên sâu VPC, S3 và IAM Security

### Mục tiêu tuần 2:
* Nắm vững kiến thức mạng ảo Amazon Virtual Private Cloud (VPC) để tự thiết kế mô hình mạng an toàn.
* Tìm hiểu cơ chế lưu trữ đối tượng không giới hạn Amazon Simple Storage Service (S3) và phân quyền bảo mật IAM.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------------ | --------------------------------------------------------------------------------- |
| 2 | - Nghiên cứu mô hình mạng ảo VPC:<br>&emsp; + IP Address CIDR Blocks<br>&emsp; + Public Subnets và Private Subnets<br>&emsp; + Route Tables, Internet Gateway (IGW)<br>&emsp; + Khác biệt giữa Network ACL (NACL) và Security Group | 24/04/2026 | 24/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 3 | - Tìm hiểu Amazon S3:<br>&emsp; + S3 Buckets, Objects, Metadata<br>&emsp; + Cấu hình chính sách bảo mật S3 Bucket Policy<br>&emsp; + Tìm hiểu tính năng Static Website Hosting | 25/04/2026 | 25/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 4 | - Tìm hiểu dịch vụ định danh và kiểm soát truy cập IAM:<br>&emsp; + IAM Users, Groups, Roles<br>&emsp; + IAM Policies (Managed Policies và Inline Policies)<br>&emsp; + Nguyên lý bảo mật Least Privilege (Quyền truy cập tối thiểu) | 26/04/2026 | 26/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 5 | - **Thực hành thực tế mạng ảo:**<br>&emsp; + Khởi tạo một VPC tùy chỉnh có CIDR `10.0.0.0/16`<br>&emsp; + Chia 2 subnets (1 Public và 1 Private)<br>&emsp; + Tạo IGW và gắn vào Public Subnet thông qua Route Table<br>&emsp; + Khởi chạy 1 EC2 nằm trong Public Subnet để kiểm tra kết nối internet. | 27/04/2026 | 28/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| 6 | - **Thực hành thực tế lưu trữ & bảo mật:**<br>&emsp; + Tạo một S3 bucket, tải tệp tin HTML tĩnh lên.<br>&emsp; + Kích hoạt tính năng Static Website Hosting.<br>&emsp; + Viết một chính sách S3 Bucket Policy dưới dạng JSON để mở quyền đọc công khai (Public Read) cho tệp tin của website tĩnh. | 29/04/2026 | 29/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |

### Kết quả đạt được tuần 2:
*   **Về kiến thức:** Hiểu sâu sắc cách phân chia lớp mạng trong hạ tầng Cloud để cô lập cơ sở dữ liệu quan trọng trong mạng Private khỏi sự xâm nhập của hacker ngoài internet. Nắm rõ cách viết chính sách phân quyền IAM dạng JSON.
*   **Về thực hành:**
    *   Thiết lập mạng VPC hoạt động ổn định với cơ chế định tuyến qua Internet Gateway chính xác.
    *   Tự tay viết mã nguồn JSON Bucket Policy để phân quyền truy cập công khai cho tệp tin S3, tránh lỗi phổ biến là `403 Forbidden`.
    *   Cấu hình một trang web tĩnh chạy trực tiếp trên S3 với độ trễ thấp, không cần duy trì máy chủ chạy 24/7.
    *   Khởi tạo IAM Role phục vụ cho việc cấp phép tài nguyên tự động (sau này ứng dụng để Lambda đọc ghi file trên S3).
