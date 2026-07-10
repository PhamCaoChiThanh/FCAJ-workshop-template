---
title: "Tuần 6: 27/05 - 03/06"
date: 2026-07-10
weight: 6
chapter: false
---

## Tuần 6: Xây dựng luồng phê duyệt đăng ký & tạo hợp đồng tự động (27/05 - 03/06)

### Công việc đã làm
*   Xây dựng phân hệ duyệt hồ sơ sinh viên: Quản lý hoặc Admin có thể đổi trạng thái của yêu cầu thuê phòng từ `PENDING` thành `APPROVED` hoặc `REJECTED`.
*   Viết thuật toán tự động khởi tạo thực thể Hợp đồng (`Contract`) mới cho sinh viên khi hồ sơ được duyệt thành công.
*   Cập nhật trạng thái phòng tương ứng sang `OCCUPIED` (Đang thuê) khi hợp đồng chính thức có hiệu lực để tránh việc phòng đó bị đăng ký đè.

### Chi tiết nghiệp vụ tự động hóa
Khi Admin gọi API phê duyệt:
1.  Hệ thống cập nhật trạng thái yêu cầu thuê phòng thành `APPROVED`.
2.  Tự động tính ngày ký hợp đồng là ngày hiện tại, ngày kết thúc là 1 năm sau.
3.  Tạo bản ghi hợp đồng mới với mã hợp đồng ngẫu nhiên.
4.  Cập nhật thuộc tính `Status` của Room tương ứng từ `VACANT` sang `OCCUPIED`.

### Kết quả đạt được
*   Hệ thống hóa luồng nghiệp vụ thuê phòng tự động ở phía Backend giúp giảm bớt việc xử lý thủ công bằng giấy tờ.
