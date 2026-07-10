---
title: "Blog 1: Tối ưu chi phí hạ tầng"
date: 2026-07-10
weight: 1
chapter: false
---

## Hành trình tối ưu hóa chi phí hạ tầng AWS cho SmartDorm xuống gần 0 USD

### Giới thiệu
Khi phát triển hệ thống quản lý KTX SmartDorm, bài toán lớn nhất đặt ra là làm sao để hệ thống chạy ổn định, nhanh chóng nhưng chi phí vận hành hàng tháng phải rẻ nhất (hướng tới mức 0 USD trong giai đoạn chạy thử nghiệm).

Dưới đây là các phương pháp thực tế mà nhóm đã áp dụng:

#### 1. Triệt tiêu chi phí máy chủ chờ bằng AWS Lambda (Serverless Compute)
*   **Vấn đề:** Nếu thuê máy chủ ảo EC2, nhóm sẽ phải trả tiền liên tục 24/7 kể cả khi ban đêm không có sinh viên truy cập.
*   **Giải pháp:** Chuyển toàn bộ Backend C# sang chạy trên AWS Lambda theo mô hình Serverless. Hệ thống chỉ tính tiền theo mili-giây khi xử lý yêu cầu thực tế, tận dụng gói miễn phí 1 triệu request/tháng của AWS Free Tier.

#### 2. Sử dụng HTTP API Gateway thay vì REST API Gateway
*   Cổng API HTTP API (API Gateway v2) giúp giảm tới 70% chi phí so với REST API Gateway truyền thống, đồng thời mang lại độ trễ thấp hơn nhiều.

#### 3. Loại bỏ hoàn toàn NAT Gateway (Tiết kiệm $32/tháng)
*   **Vấn đề:** Để Lambda kết nối an toàn tới RDS PostgreSQL nằm trong mạng nội bộ, thông thường AWS yêu cầu cấu hình NAT Gateway để Lambda truy cập internet, tốn phí cố định lớn.
*   **Giải pháp:** Thiết lập định tuyến thông minh và kết nối trực tiếp thông qua Security Groups giữa Lambda và RDS, loại bỏ NAT Gateway.

#### 4. Sử dụng RDS PostgreSQL chạy trên dòng chip ARM (db.t4g.micro)
*   Tận dụng gói miễn phí RDS 750 giờ/tháng chạy trên bộ xử lý Graviton2 (ARM) mạnh mẽ và tiết kiệm điện năng của AWS.
