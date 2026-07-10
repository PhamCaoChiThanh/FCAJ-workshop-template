---
title: "Tuần 3: 03/05 - 10/05"
date: 2026-07-10
weight: 3
chapter: false
---

## Tuần 3: Khởi động dự án SmartDorm & Thiết kế cơ sở dữ liệu (03/05 - 10/05)

### Công việc đã làm
*   Thực hiện họp thống nhất ý tưởng dự án thực hành cá nhân: **SmartDorm** (Ứng dụng quản lý kí túc xá thông minh).
*   Phân tích các yêu cầu chức năng: Quản lý thông tin phòng, tiếp nhận yêu cầu đặt phòng từ sinh viên, phê duyệt tạo hợp đồng và quản lý điện nước hàng tháng.
*   Thiết kế Sơ đồ thực thể mối quan hệ Database (Entity Relationship Diagram - ERD) bao gồm các thực thể chính: Room, Tenant, Contract, Invoice, UtilityUsage, Maintenance và Vehicle.
*   Khởi tạo Git repository cho dự án và thiết lập môi trường phát triển cục bộ.

### Chi tiết kỹ thuật thiết kế Database
Chúng tôi đã thiết kế CSDL quan hệ trên PostgreSQL để tối ưu việc lưu trữ và truy vấn:
*   Bảng `Rooms` lưu số phòng, đơn giá, số lượng giường và trạng thái phòng (Vacant/Occupied).
*   Bảng `Tenants` lưu thông tin cá nhân của sinh viên thuê phòng (Số điện thoại, ảnh căn cước công dân CCCD, email).
*   Bảng `Contracts` tạo liên kết khóa ngoại giữa `Rooms` và `Tenants` để ghi nhận thời hạn thuê.
*   Bảng `Invoices` chứa thông tin hóa đơn hàng tháng bao gồm tiền phòng, điện nước và phí phát sinh.
*   Bảng `UtilityUsage` ghi nhận chỉ số điện nước (chữ điện, khối nước) đầu kỳ và cuối kỳ của từng phòng theo tháng.

### Kết quả đạt được
*   Hoàn thành sơ đồ quan hệ ERD trực quan.
*   Khởi tạo thành công repository GitHub để phục vụ cho việc lập trình Backend và Frontend.
