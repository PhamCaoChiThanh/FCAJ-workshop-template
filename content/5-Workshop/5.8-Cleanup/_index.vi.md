---
title: "5.8 Dọn dẹp hạ tầng"
date: 2026-07-10
weight: 8
chapter: false
---

# 5.8 Dọn dẹp tài nguyên hạ tầng đám mây

Sau khi hoàn thành buổi báo cáo thử nghiệm dự án hoặc kết thúc đợt học tập thực hành lớn, việc thu hồi và xóa sạch các tài nguyên đã khởi tạo trên đám mây AWS là một bước cực kỳ quan trọng để đảm bảo an toàn tài chính.

Các dịch vụ như Amazon RDS PostgreSQL và S3 mặc dù nằm trong gói Free Tier nhưng có những giới hạn dung lượng và thời gian hoạt động cố định mỗi tháng. Nếu bạn bỏ quên hệ thống chạy ngầm liên tục mà không sử dụng, tài khoản AWS của bạn có thể phát sinh hóa đơn tiền điện toán ngoài ý muốn.

Nhờ cấu hình hạ tầng bằng Terraform IaC, bạn chỉ cần thực thi một câu lệnh duy nhất để tự động giải phóng toàn bộ tài nguyên đã tạo.

---

### 1. Quy trình chạy lệnh dọn dẹp hạ tầng tự động

Mở Command Prompt hoặc PowerShell trên máy tính cá nhân của bạn, di chuyển vào thư mục chứa các file cấu hình Terraform (`terraform/`) và thực thi câu lệnh duy nhất sau:

```bash
terraform destroy -var="db_password=Chithanh123plus" -auto-approve
```

*Giải thích các tham số truyền vào:*
*   `-var="db_password=..."`: Cung cấp lại mật khẩu nhạy cảm để xác thực quyền quản trị database trong quá trình thu hồi tài nguyên trên AWS RDS.
*   `-auto-approve`: Bỏ qua bước xác nhận đồng ý thủ công bằng cách gõ `yes` trên terminal, giúp quy trình dọn dẹp tự động hóa hoàn toàn.

---

### 2. Tiến trình thu hồi tài nguyên thực tế của AWS

Khi nhận được lệnh hủy bỏ, Terraform sẽ tự động phân tích cây phụ thuộc ngược (Dependency Tree) của hạ tầng và tiến hành hủy bỏ các tài nguyên theo trình tự an toàn nhất để tránh lỗi khóa chéo:

1.  **Hủy liên kết mạng:** Hủy bỏ các liên kết định tuyến Route Table, ngắt kết nối dải IP Subnet A và Subnet B khỏi cổng Internet Gateway.
2.  **Thu hồi RDS Database:** Tiến hành đóng tất cả các kết nối đang hoạt động của máy chủ RDS PostgreSQL và tiến hành xóa máy chủ (Quá trình này tốn khoảng **3 đến 5 phút** để AWS dọn dẹp phân vùng lưu trữ đĩa cứng ảo).
3.  **Xóa AWS Lambda & API Gateway:** Thu hồi hàm Lambda xử lý API Backend, xóa bỏ dải phân quyền IAM Role, xóa các Endpoint địa chỉ HTTPS Function URL và HTTP API Gateway.
4.  **Giải phóng bộ nhớ S3 Buckets:** Tiến hành xóa sạch các tệp nén mã nguồn trong deployment bucket và giải phóng S3 bucket hosting.
5.  **Màn hình sẽ hiển thị kết quả thành công cuối cùng:**
    ```text
    Destroy complete! Resources: 18 destroyed.
    ```

---

> [!CAUTION]
> **CẢNH BÁO MẤT DỮ LIỆU VĨNH VIỄN:**
> Việc thực thi lệnh `terraform destroy` sẽ xóa bỏ hoàn toàn tất cả các bảng dữ liệu, ràng buộc khóa ngoại và toàn bộ thông tin sinh viên, hóa đơn, lịch sử thanh toán trên máy chủ RDS PostgreSQL đám mây. Hãy chắc chắn rằng bạn đã thực hiện sao lưu cơ sở dữ liệu (Database Backup) ra file cục bộ trước khi thực thi lệnh xóa này nếu đây là các dữ liệu kiểm thử quan trọng.
