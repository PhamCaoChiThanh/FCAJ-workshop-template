---
title: "Tuần 12: 13/07 - 19/07"
date: 2026-07-10
weight: 12
chapter: false
---

## Tuần 12: Tích hợp Amazon S3 cho File Upload & Triển khai URL Masking Security (13/07 - 19/07)

### Công việc đã làm
*   Khắc phục lỗi ghi đĩa do AWS Lambda có hệ thống tệp chỉ đọc (Read-only filesystem) bằng cách tích hợp SDK Amazon S3 vào controller tải file để đẩy ảnh thẳng lên S3 Bucket công khai.
*   Cài đặt phân quyền IAM policy cho phép Lambda tải tệp tin lên S3 Bucket thông qua Terraform.
*   Triển khai cơ chế URL Masking (Che giấu URL) tại Frontend Next.js. Toàn bộ các trang quản trị được che giấu URL trên thanh địa chỉ thành mã băm an toàn dạng `/?sig=xxx&id=yyy` để tránh việc người ngoài nhìn thấy cấu trúc URL gốc.
*   Đưa dự án Frontend lên deploy chính thức tại nền tảng Vercel Hosting.

### Kết quả đạt được
*   Hệ thống SmartDorm chạy ổn định end-to-end trên AWS với mức chi phí vận hành hàng tháng tối ưu gần như 0 USD.
