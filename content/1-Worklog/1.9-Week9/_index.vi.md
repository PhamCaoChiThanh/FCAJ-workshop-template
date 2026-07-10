---
title: "Tuần 9: 15/06 - 21/06"
date: 2026-07-10
weight: 9
chapter: false
---

## Tuần 9: Liên kết JWT Authentication & Kết nối Frontend - Backend (15/06 - 21/06)

### Công việc đã làm
*   Tích hợp bảo mật JWT (JSON Web Token) cho các API Backend cần phân quyền.
*   Kết nối Frontend Next.js với Backend APIs bằng cách viết các hàm fetch trong thư mục `lib/api.ts`.
*   Lưu trữ JWT token an toàn trong `sessionStorage` trên client-side để tự động đính kèm vào header `Authorization: Bearer <token>` cho các request tiếp theo.
*   Tối ưu hóa giao diện: Hỗ trợ Light/Dark mode và giao diện co giãn Responsive tốt trên mọi kích thước màn hình.

### Kết quả đạt được
*   Khách truy cập không thể xem các trang bảo mật nếu chưa đăng nhập.
*   Giao diện kết nối dữ liệu API thời gian thực hoàn tất (lấy đúng thông tin cá nhân và hóa đơn của người dùng đang đăng nhập).
