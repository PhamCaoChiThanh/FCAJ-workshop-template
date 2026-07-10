---
title: "Blog 2: Lỗi Read-Only Lambda"
date: 2026-07-10
weight: 2
chapter: false
---
## Xử lý lỗi Read-Only Filesystem trên AWS Lambda với Amazon S3
Khi chạy API ASP.NET Core Web API trên AWS Lambda, mọi cố gắng lưu tệp trực tiếp vào thư mục cục bộ đều gặp lỗi \`Access to the path is denied\` vì hệ thống tệp Lambda là Read-only. Bài viết hướng dẫn cấu hình AWS SDK cho S3 trong .NET và tạo S3 Bucket qua Terraform để xử lý lưu trữ file ảnh đại diện và CCCD một cách an toàn.
