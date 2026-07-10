---
title: "Tuần 4: 11/05 - 18/05"
date: 2026-07-10
weight: 4
chapter: false
---

## Tuần 4: Thiết lập cấu trúc dự án Backend ASP.NET Core & kết nối PostgreSQL local (11/05 - 18/05)

### Công việc đã làm
*   Khởi tạo dự án Web API sử dụng ASP.NET Core (.NET 10).
*   Thiết lập mô hình MVC / Clean Architecture chia tách rõ ràng:
    *   `Controllers`: Tiếp nhận yêu cầu HTTP.
    *   `Models`: Định nghĩa cấu trúc dữ liệu và thực thể ánh xạ database.
    *   `Services`: Xử lý logic nghiệp vụ.
*   Cấu hình Entity Framework Core (EF Core) và viết DbContext để quản lý các migrations.
*   Cài đặt Docker và chạy container PostgreSQL cục bộ để làm database thử nghiệm ở local.

### Chi tiết triển khai code
*   Cấu hình chuỗi kết nối (Connection String) bảo mật thông qua file cấu hình `appsettings.json`.
*   Viết code khởi tạo dữ liệu mặc định (Seed Data) cho danh sách phòng để khi khởi chạy hệ thống, database tự động điền sẵn dữ liệu thử nghiệm.
*   Chạy các câu lệnh migration:
    ```bash
    dotnet ef migrations add InitialCreate
    dotnet ef database update
    ```

### Kết quả đạt được
*   Khung dự án Web API hoạt động ổn định trên local.
*   Database PostgreSQL cục bộ được khởi chạy thành công thông qua Docker với đầy đủ cấu trúc bảng.
