---
title: "Tuần 5: 18/05 - 24/05"
date: 2026-07-10
weight: 5
chapter: false
---

## Tuần 5: Xây dựng CRUD APIs quản lý phòng & hồ sơ yêu cầu thuê phòng (18/05 - 24/05)

### Công việc đã làm
*   Phát triển các API CRUD cho thực thể Phòng (`/api/rooms`) giúp quản trị viên thêm, sửa, xóa, tìm kiếm thông tin phòng.
*   Xây dựng API cho phép sinh viên (Tenant) đăng ký thông tin cá nhân và gửi yêu cầu đăng ký thuê phòng trực tuyến (`/api/requests`).
*   Viết validator để kiểm tra tính đúng đắn của dữ liệu đầu vào (định dạng email, độ dài CCCD, số điện thoại Việt Nam).

### Chi tiết các Endpoint
*   `GET /api/rooms`: Lấy danh sách toàn bộ phòng trọ, lọc theo trạng thái trống/đã thuê.
*   `POST /api/rooms`: Quản trị viên thêm phòng mới vào hệ thống.
*   `POST /api/requests`: Sinh viên điền đơn đăng ký thuê phòng, dữ liệu lưu vào bảng `TenantRequests` ở trạng thái `PENDING`.

### Kết quả đạt được
*   Đầy đủ bộ API quản lý phòng và tiếp nhận hồ sơ đăng ký hoạt động chính xác. Kiểm thử APIs thành công thông qua Swagger UI.
