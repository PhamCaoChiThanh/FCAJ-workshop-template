---
title: "Đề xuất dự án"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 2. </b> "
---

## Đề xuất dự án: SmartDorm - Hệ thống quản lý KTX thông minh

### 1. Tổng quan dự án
SmartDorm là giải pháp số hóa toàn diện việc quản lý ký túc xá và nhà trọ lớn, giúp giải quyết các rào cản hành chính giữa chủ nhà/quản trị viên và sinh viên thuê phòng.

### 2. Mục tiêu dự án
*   **Số hóa quy trình:** Chuyển đổi toàn bộ việc làm hợp đồng giấy, hóa đơn thủ công thành quy trình tự động trực tuyến.
*   **Tiết kiệm năng lượng & Giám sát chặt chẽ:** Quản lý số liệu điện nước trực quan hàng tháng, hạn chế thất thoát dữ liệu.
*   **Tối ưu hóa chi phí vận hành:** Đạt mức chi phí lưu trữ đám mây tối thiểu (~0 USD/tháng trong giai đoạn thử nghiệm).

### 3. Vấn đề cần giải quyết
*   Mất thời gian quản lý hồ sơ giấy tờ, dễ sai lệch số liệu điện nước khi đo đạc thủ công.
*   Sự chậm trễ trong khâu thanh toán và gửi thông báo tới sinh viên.
*   Chi phí duy trì máy chủ ảo truyền thống (EC2/VPS) đắt đỏ, lãng phí tài nguyên khi không có người truy cập ban đêm.

### 4. Kiến trúc giải pháp (AWS Cloud)
Hệ thống sử dụng các dịch vụ AWS tối ưu chi phí:
*   **API Gateway (HTTP API):** Cổng API trung gian chi phí thấp.
*   **AWS Lambda:** Xử lý logic nghiệp vụ serverless (C# .NET Core), tính phí theo thời gian chạy thực tế.
*   **Amazon S3:** Lưu trữ tĩnh mã nguồn frontend và tệp ảnh đại diện/CCCD được tải lên từ người dùng.
*   **Amazon RDS (PostgreSQL):** Cơ sở dữ liệu quan hệ được quản lý hoàn toàn, chạy dòng chip Graviton2 tối ưu hiệu năng.

### 5. Timeline dự án (12 Tuần)
*   **Tuần 1-4:** Thiết kế database, viết APIs Core quản lý phòng và tiếp nhận yêu cầu.
*   **Tuần 5-8:** Phát triển thuật toán tính điện nước, phân hệ Hóa đơn, xây dựng giao diện Next.js, kết nối API và Test local.
*   **Tuần 9-12:** Viết kịch bản Terraform IaC, deploy hạ tầng AWS RDS, Lambda Serverless, tích hợp S3 và Deploy frontend lên Vercel.

### 6. Rủi ro & Giải pháp phòng ngừa
*   *Lỗi ghi đĩa Lambda:* Lambda có hệ thống tệp chỉ đọc. Giải quyết bằng cách tích hợp Amazon S3 để lưu file trực tiếp.
*   *Lỗi CORS:* Cấu hình API Gateway CORS headers chi tiết để bảo vệ tài nguyên nhưng vẫn cho phép client Vercel gọi API.
