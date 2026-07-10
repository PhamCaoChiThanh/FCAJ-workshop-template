---
title: "AWS & Serverless vận hành SmartDorm"
date: 2026-07-10
weight: 1
chapter: false
---

## AWS & Serverless đã giúp nhóm mình vận hành hệ thống quản lý Ký túc xá (SmartDorm) như thế nào?

Xin chào mọi người! Hôm nay mình muốn chia sẻ thực tế cách nhóm mình áp dụng các dịch vụ đám mây AWS để vận hành toàn bộ nghiệp vụ của hệ thống quản lý KTX SmartDorm, đồng thời giải quyết bài toán lớn về chi phí và bảo mật.

Thay vì giải thích công nghệ là gì, bài viết này sẽ tập trung vào việc những công nghệ đó đã làm được những gì trong dự án của tụi mình.

### 1. Tự động hóa tính tiền điện nước và xuất hóa đơn phòng (AWS Lambda Node.js/C#)

Trong dự án SmartDorm, các chức năng tính toán được phân chia rõ ràng để đảm bảo hệ thống luôn xử lý tức thời:
*   **Xử lý lõi ứng dụng (C# Web API):** Đảm nhận toàn bộ luồng đăng ký tài khoản, phân quyền (Chủ nhà - Quản lý - Khách thuê), ký kết hợp đồng điện tử và quản lý tình trạng phòng trống.
*   **Tự động tính hóa đơn dịch vụ (utilityHandler.js):** Khi quản lý nhập chỉ số điện nước mới, hệ thống tự động so khớp với chỉ số cũ để tính lượng tiêu thụ thực tế và quy đổi thành tiền theo đơn giá lũy tiến của từng phòng.
*   **Xuất hóa đơn tổng hợp (invoiceHandler.js):** Cuối tháng, hệ thống tự động gom tiền phòng, tiền điện nước tiêu thụ, tiền rác và phí gửi xe phát sinh của phòng đó để xuất một hóa đơn tổng duy nhất gửi tới khách thuê.

### 2. Quản lý chặt chẽ thông tin thuê phòng và bãi giữ xe (Amazon RDS PostgreSQL)

Toàn bộ luồng dữ liệu quan hệ phức tạp của KTX được tổ chức đồng bộ để tránh thất thoát:
*   **Liên kết dữ liệu:** Kết nối chặt chẽ thông tin của Khách thuê -> Hợp đồng thuê phòng -> Tiền đặt cọc -> Hóa đơn hàng tháng.
*   **Hệ thống gửi xe thông minh (vehicleHandler.js):** Lưu trữ thông tin phương tiện của từng phòng, quản lý trạng thái duyệt vé xe tháng/ngày và liên kết trực tiếp phí giữ xe vào hóa đơn phòng của tháng đó.
*   **Nhật ký vận hành (Audit Logs):** Ghi lại chi tiết ai đã chỉnh sửa chỉ số điện nước, ai đã thay đổi thông tin hợp đồng hay duyệt yêu cầu sửa chữa thiết bị, giúp chủ KTX dễ dàng đối soát khi xảy ra tranh chấp.

### 3. Lưu trữ tài liệu xác minh và chạy giao diện người dùng (Amazon S3)

*   **Lưu trữ hồ sơ khách thuê:** Khi khách thuê làm thủ tục nhận phòng, hình ảnh CCCD và ảnh chụp phương tiện đăng ký gửi xe được tải lên và lưu trữ an toàn trên S3, sẵn sàng hiển thị cho ban quản lý đối chiếu khi cần.
*   **Vận hành giao diện (Next.js Static Hosting):** Toàn bộ giao diện tương tác của chủ nhà (Owner Dashboard) và khách thuê (Tenant Portal) được lưu trữ và chạy trực tiếp trên S3, giúp trang web tải cực nhanh và hoạt động độc lập mà không cần duy trì máy chủ web.

### 4. Giải quyết bài toán bảo mật và chi phí hạ tầng (Kiến trúc Tối ưu của nhóm)

*   **Đưa chi phí vận hành về 0$:** Bằng cách chuyển toàn bộ logic xử lý sang chạy theo sự kiện (Event-driven) và loại bỏ các dịch vụ trung gian có phí cố định (như API Gateway, NAT Gateway), dự án SmartDorm có thể chạy thử nghiệm thực tế cho hàng trăm phòng trọ mà không phát sinh bất kỳ chi phí duy trì hạ tầng nào hàng tháng.
*   **Cô lập dữ liệu tuyệt đối:** Nhóm đã cấu hình chặn toàn bộ truy cập từ internet vào database. Chỉ những đoạn code xử lý nghiệp vụ được cấp phép trên hệ thống mới có quyền đọc/ghi dữ liệu, đảm bảo thông tin cá nhân và lịch sử đóng tiền của khách thuê không bị rò rỉ ra ngoài.

### Kết luận

Qua dự án này, nhóm mình đã hiện thực hóa được một hệ thống quản lý KTX vận hành hoàn toàn tự động từ khâu tính tiền dịch vụ, quản lý bãi giữ xe cho đến xử lý báo hỏng. Việc ứng dụng đúng vai trò của từng dịch vụ AWS đã giúp nhóm giải quyết được đồng thời hai bài toán hóc búa nhất: Vận hành chính xác và Chi phí hạ tầng bằng 0$.
