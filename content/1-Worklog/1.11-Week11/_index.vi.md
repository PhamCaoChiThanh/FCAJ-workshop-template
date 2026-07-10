---
title: "Tuần 11: 29/06 - 05/07"
date: 2026-07-10
weight: 11
chapter: false
---

## Tuần 11: Deploy Backend lên AWS Lambda Serverless & Giải quyết lỗi CORS (29/06 - 05/07)

### Công việc đã làm
*   Biên dịch và đóng gói Backend C# thành tệp bootstrap zip tương thích với môi trường Serverless của **AWS Lambda**.
*   Tải mã nguồn zip lên S3 bucket phục vụ triển khai và kết nối Lambda với cổng **AWS API Gateway** (sử dụng loại HTTP API v2 tốc độ cao và chi phí rẻ).
*   Khắc phục triệt để lỗi CORS phát sinh khi Frontend gọi API HTTPS chéo nguồn.

### Chi tiết cấu hình Serverless
*   Ứng dụng được chạy mà không cần cấu hình cụ thể tài nguyên RAM và CPU cố định, giảm thiểu hoàn toàn chi phí khi hệ thống không hoạt động.
*   Cấu hình API Gateway CORS Policy cho phép phương thức `GET, POST, PUT, DELETE` và cho phép Header `Content-Type, Authorization`.

### Kết quả đạt được
*   Backend API chạy thành công ở mô hình Serverless trên AWS Lambda, giúp loại bỏ 100% chi phí máy chủ chờ.
