---
title: "Tuần 7: 04/06 - 11/06"
date: 2026-07-10
weight: 7
chapter: false
---

## Tuần 7: Thuật toán tính chỉ số điện nước & lập hóa đơn hàng tháng (04/06 - 11/06)

### Công việc đã làm
*   Thiết kế bảng dữ liệu `UtilityUsages` lưu chỉ số điện (kWh) và nước (m3) tiêu thụ thực tế hàng tháng của từng phòng.
*   Xây dựng thuật toán tính tiền phòng, tiền điện, tiền nước tiêu thụ thực tế cùng các loại phí dịch vụ đi kèm khi tạo Hóa đơn (Invoice) mỗi tháng.
*   Viết API lập hóa đơn tự động và cập nhật chỉ số điện nước đầu vào.

### Thuật toán tính toán
Tổng tiền hóa đơn được tính theo công thức:
```text
Tổng tiền = Tiền phòng cơ bản 
           + (Chỉ số điện cuối - Chỉ số điện đầu) * Đơn giá điện 
           + (Chỉ số nước cuối - Chỉ số nước đầu) * Đơn giá nước 
           + Phí dịch vụ cố định
```
Trạng thái hóa đơn ban đầu sẽ là `UNPAID` (Chưa thanh toán) và sẽ chuyển thành `PAID` khi người quản lý xác nhận đã nhận chuyển khoản thành công.

### Kết quả đạt được
*   Hệ thống tính toán chuẩn xác hóa đơn cho từng phòng dựa theo số chữ điện, nước tiêu thụ thực tế mà không cần tính tay.
