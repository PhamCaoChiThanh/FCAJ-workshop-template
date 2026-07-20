---
title: "5.1 Tổng quan dự án"
date: 2026-07-10
weight: 1
chapter: false
---

# 5.1 Tổng quan dự án & Sơ đồ kiến trúc hệ thống

### 1. Bối cảnh thực tế và Động lực kỹ thuật (Business & Technical Drivers)

Trong mô hình vận hành các khu ký túc xá (KTX) thông minh hay các tòa nhà căn hộ dịch vụ, hệ thống quản lý trực tuyến đóng vai trò làm huyết mạch kết nối giữa Ban quản lý và Khách thuê. Hệ thống phải đảm bảo trực tuyến liên tục 24/7 để đáp ứng các nhu cầu tức thời như:
*   **Sinh viên/Khách thuê:** Đăng ký phòng mới, nộp thông tin phương tiện, xem hóa đơn tiền phòng/điện/nước và báo cáo sự cố kỹ thuật (hỏng bóng đèn, vỡ đường nước).
*   **Chủ nhà/Ban quản lý:** Tiếp nhận yêu cầu, lập hóa đơn tự động hàng tháng, phê duyệt hợp đồng thuê và gửi thông báo nhắc nợ.

Tuy nhiên, phân tích hành vi người dùng thực tế cho thấy sự biến động cực lớn về lưu lượng truy cập:
*   **Khung giờ cao điểm (Peak Hours):** Thường rơi vào thời điểm đầu tháng khi đối soát hóa đơn hoặc đầu học kỳ khi sinh viên đăng ký phòng mới.
*   **Khung giờ thấp điểm (Off-peak Hours):** Chiếm tới 70% thời gian hoạt động (đặc biệt từ 11h đêm đến 6h sáng hôm sau, hoặc các kỳ nghỉ hè/nghỉ Tết).

Nếu hệ thống được triển khai trên các máy chủ truyền thống chạy liên tục như **Amazon EC2** hay VPS thông thường, doanh nghiệp hoặc nhà trường vẫn phải chi trả chi phí cố định hàng tháng (~$15 đến $40/tháng cho các tài nguyên nhàn rỗi). Để giải quyết bài toán lãng phí này, **Secure SmartDorm** áp dụng mô hình điện toán không máy chủ **Serverless Architecture** trên nền tảng AWS. Mô hình này giúp:
1.  **Tối ưu hóa chi phí vượt trội:** Tài nguyên tính toán chỉ được khởi chạy và tính phí khi có yêu cầu thực tế gửi tới (Pay-as-you-go). Chi phí nhàn rỗi hoàn toàn bằng **0 USD**.
2.  **Khả năng tự động mở rộng (Auto-scaling):** Hệ thống có thể tự động tăng số lượng container xử lý từ 0 lên hàng trăm container chỉ trong vài giây để đáp ứng lượng truy cập lớn đầu tháng, sau đó tự động co về 0 khi lưu lượng giảm.
3.  **Giảm thiểu gánh nặng vận hành (No Server Management):** AWS chịu trách nhiệm quản trị, vá lỗi bảo mật hệ điều hành và cập nhật phần cứng, giúp nhóm phát triển tập trung hoàn toàn vào mã nguồn nghiệp vụ.

---

### 2. Sơ đồ kiến trúc tổng thể (Architecture Diagram)

Dưới đây là mô hình kiến trúc phân tầng, phân vùng bảo mật an toàn mạng (Network Zoning) và luồng đi của dữ liệu từ thiết bị người dùng đến các dịch vụ đám mây AWS:

![1783657347600](image/_index/1783657347600.png)

---

### 3. Phân tích chi tiết vai trò của các dịch vụ đám mây áp dụng

Kiến trúc dự án SmartDorm được chia làm 4 tầng logic độc lập:

| Tầng chức năng | Công nghệ áp dụng | Cơ chế hoạt động & Vai trò nghiệp vụ |
| :--- | :--- | :--- |
| **Presentation Layer (Tầng giao diện)** | **Next.js 16 (React) / Vercel** | Giao diện Single Page Application (SPA) mượt mà, phản hồi responsive tốt. Được compile tĩnh (Static Export) và phân phối qua mạng CDN toàn cầu của Vercel, đảm bảo tốc độ tải trang cực nhanh và an toàn tuyệt đối trước các đợt tấn công từ chối dịch vụ (DDoS) vào frontend. |
| **API Gate / Exposure Layer** | **API Gateway / Function URL** | Cổng giao tiếp công khai trung gian. Nhận các request HTTPS từ Next.js, thực hiện lọc các header cơ bản và chuyển tiếp dữ liệu dạng JSON vào hàm AWS Lambda một cách an toàn nhất. |
| **Compute Layer (Tầng tính toán)** | **AWS Lambda (C# ASP.NET Core)** | Xử lý logic nghiệp vụ trung tâm. Sử dụng C# .NET 10 cho hiệu năng biên dịch tối ưu. Khi có request, Lambda khởi dựng một micro-VM container để chạy mã API xử lý (như tính toán hóa đơn, xác thực JWT, tạo hợp đồng) rồi lập tức trả kết quả về cho client. |
| **Database Layer (Tầng lưu trữ dữ liệu)** | **Amazon RDS PostgreSQL** | Cơ sở dữ liệu quan hệ quản trị. Đặt trong phân vùng mạng riêng biệt (Private Subnet). Toàn bộ dữ liệu nhạy cảm được cấu hình ràng buộc khóa ngoại chặt chẽ, chỉ chấp nhận truy vấn từ dải IP an toàn của Lambda. |
| **Media Storage (Tầng lưu trữ tệp)** | **Amazon S3** | Kho chứa tệp tin tĩnh (ảnh đại diện, hóa đơn, CCCD). Do đĩa cứng của Lambda bị khóa ở chế độ Read-only, S3 đóng vai trò là nơi lưu trữ lâu bền với dung lượng không giới hạn và độ an toàn dữ liệu lên tới 99.999999999%. |
| **AI Integration (Tầng trí tuệ nhân tạo)** | **Amazon Bedrock** | Trí tuệ nhân tạo tạo sinh (Generative AI). Đóng vai trò là trợ lý ảo tự động phân tích hành vi thanh toán trễ hạn của sinh viên, soạn thảo nội dung email nhắc nợ tự động được cá nhân hóa sâu sắc theo ngữ cảnh để thúc giục thanh toán hiệu quả. |
