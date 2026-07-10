---
title: "Cấu hình hạ tầng AWS SmartDorm ~0đ"
date: 2026-07-10
weight: 2
chapter: false
---

## Cách nhóm mình cấu hình hạ tầng AWS cho SmartDorm với chi phí gần về 0 đồng

Xin chào mọi người! Khi bắt tay vào xây dựng hệ thống quản lý KTX SmartDorm, mục tiêu lớn nhất mà nhóm mình hướng tới là làm sao để vận hành hệ thống thật mượt mà, bảo mật nhưng chi phí duy trì hàng tháng phải rẻ nhất có thể, lý tưởng nhất là tiệm cận 0 USD trong giai đoạn chạy thử nghiệm.

Sau nhiều lần thử nghiệm và tối ưu hạ tầng, đây là những cách thực tế mà tụi mình đã áp dụng để đạt được mục tiêu đó:

### 1. Tránh lãng phí tài nguyên bằng cách chạy serverless trên AWS Lambda

Thay vì thuê một máy chủ ảo (như EC2 hay VPS) chạy liên tục 24/7 ngay cả khi không có ai truy cập, nhóm mình quyết định đưa toàn bộ Backend C# sang chạy dạng Serverless trên AWS Lambda. Nhờ cơ chế này, code chỉ thực sự khởi chạy và tính phí theo mili-giây khi có yêu cầu từ người dùng gửi đến và tự động tắt ngay sau khi xử lý xong. Tận dụng định mức miễn phí 1 triệu request/tháng của gói AWS Free Tier, tụi mình hoàn toàn không tốn một đồng nào cho máy chủ tính toán.

### 2. Tiết kiệm hơn nhờ cổng kết nối API Gateway phiên bản HTTP API (v2)

Để làm cầu nối trung chuyển dữ liệu từ giao diện đến Backend, thay vì dùng cổng REST API cũ có chi phí cao, tụi mình đã lựa chọn cấu hình HTTP API. Lựa chọn này không chỉ giúp giảm tới 70% chi phí kết nối trên lý thuyết mà thực tế còn mang lại tốc độ phản hồi API cực kỳ nhanh, giúp trải nghiệm của sinh viên và chủ nhà mượt mà hơn.

### 3. Tận dụng lưu trữ tĩnh trên Amazon S3 và Vercel để chạy giao diện

Với giao diện Next.js, nếu chạy theo cách thông thường sẽ cần một server Node.js hoạt động liên tục để dựng trang, cực kỳ tốn RAM và CPU. Nhóm mình đã chọn cách đóng gói toàn bộ giao diện thành mã nguồn tĩnh (Static Export) rồi đưa lên lưu trữ trên Amazon S3 và deploy qua Vercel. Giải pháp này loại bỏ hoàn toàn chi phí máy chủ chạy Frontend, tận dụng được gói hosting tĩnh đi kèm chứng chỉ bảo mật SSL (HTTPS) hoàn toàn miễn phí của Vercel.

### 4. Chọn dòng chip Graviton2 (ARM64) tối ưu cho cơ sở dữ liệu RDS

Cơ sở dữ liệu thường là phần ngốn nhiều ngân sách nhất của mọi hệ thống. Để giải quyết bài toán này, tụi mình đã cấu hình database Amazon RDS PostgreSQL sử dụng dòng instance chạy chip ARM mang mã db.t4g.micro. Dòng chip này vừa mang lại hiệu năng xử lý truy vấn dữ liệu vượt trội, vừa nằm trọn vẹn trong diện miễn phí 750 giờ/tháng của gói AWS Free Tier trong năm đầu tiên.

### 5. Cắt giảm $32/tháng chi phí cố định bằng cách loại bỏ NAT Gateway

Thông thường, để các dịch vụ trong mạng nội bộ gọi được ra ngoài internet, AWS sẽ yêu cầu chúng ta tạo một cổng NAT Gateway với mức phí cố định khá đắt đỏ (khoảng 32 USD/tháng). Nhóm mình đã tối ưu lại sơ đồ mạng, cho phép Lambda kết nối trực tiếp đến database thông qua cơ chế lọc cổng bảo mật Security Groups và định tuyến thông minh. Việc loại bỏ NAT Gateway giúp tụi mình tiết kiệm ngay gần 800.000 VNĐ mỗi tháng mà cơ sở dữ liệu vẫn được bảo vệ an toàn tuyệt đối khỏi internet.

### Tổng kết

Bằng việc phối hợp nhịp nhàng giữa kiến trúc Serverless, tối ưu hóa phần cứng ARM và tận dụng tối đa các gói Free Tier, nhóm mình đã vận hành trơn tru toàn bộ hệ thống quản lý KTX SmartDorm thực tế với hóa đơn hạ tầng Cloud hàng tháng bằng đúng 0 USD. Hy vọng những chia sẻ này sẽ giúp ích cho các bạn trong việc tối ưu hóa chi phí cho dự án của riêng mình!
