---
title: "Báo cáo thực tập tốt nghiệp"
date: 2026-07-10
weight: 1
chapter: false
---

<div style="border: 3px double #000; padding: 25px; margin-bottom: 30px; text-align: center; font-family: 'Times New Roman', Times, serif;">
  <p style="font-size: 15px; font-weight: bold; margin: 0; text-transform: uppercase;">Bộ Giáo dục và Đào tạo</p>
  <p style="font-size: 16px; font-weight: bold; margin: 5px 0 0 0; text-transform: uppercase;">Trường Đại học Công nghệ TP. HCM (HUTECH)</p>
  <p style="font-size: 14px; font-weight: bold; margin: 5px 0 0 0; text-transform: uppercase;">Viện Công nghệ thông tin</p>
  
  <div style="margin: 60px 0;">
    <p style="font-size: 26px; font-weight: bold; margin: 0; text-transform: uppercase; color: #002D62;">Báo cáo thực tập tốt nghiệp</p>
    <p style="font-size: 18px; font-weight: bold; margin: 15px 0 0 0; text-transform: uppercase;">Đề tài: Triển khai hạ tầng Serverless cho dự án SmartDorm trên nền tảng AWS</p>
    <p style="font-size: 16px; font-style: italic; margin: 10px 0 0 0;">Đơn vị thực tập: Amazon Web Services (AWS) Việt Nam</p>
  </div>
  
  <div style="text-align: left; margin: 80px auto 40px auto; width: 85%; font-size: 15px; line-height: 1.8;">
    <table style="width: 100%; border: none; border-collapse: collapse;">
      <tr style="border: none;">
        <td style="width: 45%; font-weight: bold; border: none; padding: 3px 0;">Ngành:</td>
        <td style="width: 55%; border: none; padding: 3px 0;">Công nghệ thông tin</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Chuyên ngành:</td>
        <td style="border: none; padding: 3px 0;">An ninh mạng</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Giảng viên hướng dẫn:</td>
        <td style="border: none; padding: 3px 0;">Đinh Huỳnh Tuệ Tuệ</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Sinh viên thực hiện:</td>
        <td style="border: none; padding: 3px 0;">Phạm Cao Chí Thành</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Mã số sinh viên (MSSV):</td>
        <td style="border: none; padding: 3px 0;">2280602954</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Lớp:</td>
        <td style="border: none; padding: 3px 0;">22DTHA3</td>
      </tr>
    </table>
  </div>
  
  <p style="font-size: 14px; margin-top: 60px;">TP. Hồ Chí Minh, Năm 2026</p>
</div>

---

## Lời cảm ơn (Acknowledgements)

Em xin gửi lời cảm ơn sâu sắc nhất tới cô **Đinh Huỳnh Tuệ Tuệ** - Giảng viên hướng dẫn trực tiếp đã luôn dành thời gian quý báu theo sát, chỉ dẫn tận tình, động viên và đưa ra những định hướng khoa học quan trọng giúp em hoàn thành đề tài báo cáo thực tập tốt nghiệp này.

Dù đã nỗ lực hết mình để hoàn thành báo cáo này một cách tốt nhất, song do kiến thức và kinh nghiệm thực tế của bản thân còn nhiều hạn chế, cuốn báo cáo chắc chắn không tránh khỏi những thiếu sót. Em rất kính mong nhận được những ý kiến đóng góp, nhận xét và chỉ dạy của cô để kiến thức của em ngày càng hoàn thiện hơn.

*Em xin chân thành cảm ơn!*

---

## Giới thiệu đơn vị thực tập (Amazon Web Services Việt Nam)

### 1. Lịch sử hình thành và phát triển
Amazon Web Services (AWS) là nền tảng điện toán đám mây toàn diện và được áp dụng rộng rãi nhất trên thế giới, cung cấp hơn 200 dịch vụ đầy đủ tính năng từ các trung tâm dữ liệu trên toàn cầu. Tại Việt Nam, AWS đã chính thức thành lập văn phòng đại diện nhằm hỗ trợ các doanh nghiệp, tổ chức chính phủ, tổ chức giáo dục và các lập trình viên tăng tốc chuyển đổi số, tối ưu hóa hạ tầng công nghệ và xây dựng các giải pháp Cloud Native hiện đại.

### 2. Lĩnh vực hoạt động chính
*   **Điện toán đám mây (Cloud Computing):** Cung cấp các dịch vụ tính toán (EC2, Lambda), lưu trữ (S3, EBS, EFS), mạng ảo bảo mật (VPC, CloudFront) và cơ sở dữ liệu quản trị (RDS, DynamoDB).
*   **Trí tuệ nhân tạo & Học máy (AI/ML):** Cung cấp các giải pháp SageMaker, Bedrock phục vụ cho Generative AI và AI tạo sinh thế hệ mới.
*   **Đào tạo nguồn nhân lực Cloud:** Thông qua các chương trình như AWS Academy và cộng đồng hỗ trợ sinh viên thực tập **First Cloud Journey (FCJ)** để đào tạo nhân lực kỹ sư đám mây chất lượng cao tại Việt Nam.

### 3. Môi trường làm việc tại AWS Việt Nam
Văn phòng làm việc của AWS Việt Nam mang đậm văn hóa đổi mới sáng tạo toàn cầu của Amazon (Leadership Principles). Quy trình làm việc chuyên nghiệp, khuyến khích tư duy sở hữu (Ownership), sẵn sàng thử nghiệm cái mới (Learn and Be Curious) và hướng tới khách hàng (Customer Obsession).

---

## PHẦN 3. PHƯƠNG PHÁP THỰC HIỆN

### 3.1. Thiết kế cơ sở dữ liệu PostgreSQL bảo mật và tối ưu
Để xây dựng một CSDL quan hệ vững chắc cho SmartDorm, sinh viên sử dụng PostgreSQL trên Amazon RDS. Sơ đồ CSDL được chuẩn hóa để loại bỏ dư thừa dữ liệu và đảm bảo tính toàn vẹn tham chiếu. 
Bảng Rooms quản lý các thuộc tính vật lý của phòng (đơn giá, số giường, trạng thái trống/đầy). Bảng Tenants đóng vai trò lưu trữ hồ sơ định danh sinh viên bao gồm cả đường dẫn ảnh CCCD phục vụ mục đích kiểm soát an ninh. Hợp đồng thuê phòng (Contracts) đóng vai trò trung gian gắn kết Rooms và Tenants dưới dạng khóa ngoại có ràng buộc thời gian hiệu lực nghiêm ngặt.
Để tự động hóa việc tính toán, bảng UtilityUsages lưu trữ lịch sử tiêu thụ điện nước đầu và cuối tháng. Dược trên dữ liệu này, phân hệ Invoices sẽ tự động lấy hiệu số tiêu thụ nhân đơn giá định mức và cộng thêm phí dịch vụ cố định, tạo ra một quy trình tính toán tự động hóa hoàn toàn, giảm thiểu sai sót do tính toán thủ công.

### 3.2. Lập trình Web API Backend bằng C# ASP.NET Core
Mã nguồn backend được xây dựng trên nền tảng .NET 10 Web API kết hợp kiến trúc Clean Architecture giúp phân định rõ ràng trách nhiệm của từng tầng logic. Lợi thế của C# .NET 10 là tốc độ khởi động nhanh và hiệu năng xử lý bất đồng bộ (async/await) xuất sắc, điều này tối ưu hóa tài nguyên RAM khi chạy trên môi trường Serverless Lambda.
Entity Framework Core được tích hợp để làm cầu nối Object-Relational Mapping (ORM), giúp ánh xạ trực tiếp các C# Class Models thành các bảng dữ liệu trong PostgreSQL mà không cần viết các câu lệnh SQL thuần phức tạp, qua đó đẩy nhanh tiến độ phát triển và giảm thiểu các lỗ hổng bảo mật dạng SQL Injection. Toàn bộ logic nghiệp vụ kiểm tra trạng thái phòng, tính tiền hóa đơn, và tự động tạo hợp đồng đều được đóng gói trong tầng Service để đảm bảo tính tái sử dụng cao.

### 3.3. Lập trình Next.js Frontend và giải pháp bảo mật URL Masking
Phần giao diện người dùng sử dụng Next.js kết hợp TailwindCSS để tạo nên một trải nghiệm UI mượt mà, phản hồi responsive tốt trên cả thiết bị di động và máy tính. Toàn bộ kết nối giữa Next.js và Backend Web API được thực hiện thông qua các API Client bất đồng bộ sử dụng phương thức fetch bảo mật bằng JSON Web Token (JWT). Token được lưu trữ tạm thời trong sessionStorage và tự động gửi kèm theo Header Authorization của mỗi HTTP Request.
Nhằm nâng cao tính an ninh mạng cho hệ thống quản trị, sinh viên đã nghiên cứu và triển khai cơ chế URL Masking (Che giấu URL) tại client-side. Thông thường, tin tặc có thể rà quét cấu trúc đường dẫn nhạy cảm để dò tìm lỗ hổng. Bằng cách mã hóa cấu trúc đường dẫn quản trị thành các chuỗi băm kèm chữ ký xác thực số dạng '/?sig=9b81d8d4&id=user', hệ thống Next.js sẽ kiểm tra tính hợp lệ của chữ ký trước khi hiển thị trang. Mọi truy cập cố tình thay đổi tham số URL mà không có chữ ký số hợp lệ sẽ bị từ chối ngay lập tức, ngăn ngừa triệt để việc truy cập bất hợp pháp.

### 3.4. Tự động hóa hạ tầng IaC bằng Terraform trên Cloud AWS
Với mục tiêu triển khai dự án chuyên nghiệp và có thể nhân bản hạ tầng nhanh chóng, sinh viên sử dụng công cụ Terraform dưới dạng mã (Infrastructure as Code - IaC). Toàn bộ hạ tầng mạng ảo VPC với CIDR 10.0.0.0/16, các phân vùng mạng Public Subnet (chứa Lambda tính toán) và Private Subnet (chứa RDS database cô lập), Internet Gateway, Route Tables đều được khai báo tường minh bằng các file cấu hình *.tf.
Cấu hình Terraform cũng định nghĩa rõ ràng Security Group của RDS PostgreSQL chỉ mở cổng 5432 nội bộ trong mạng VPC và chặn hoàn toàn các truy cập lạ từ internet để bảo vệ dữ liệu tối đa. AWS Lambda được cấu hình Custom Runtime chạy trên nền Linux x64 để tự động scale-up theo lượng requests thực tế. Việc tích hợp Terraform giúp quản trị toàn bộ tài nguyên hệ thống một cách khoa học, giảm thiểu tối đa các lỗi cấu hình sai sót bằng tay của con người.

---

## PHẦN 4. KẾT QUẢ ĐẠT ĐƯỢC VÀ ĐÓNG GÓP

### 4.1. Những nội dung kiến thức lý thuyết đã được củng cố và phát triển sâu sắc
Trải qua quá trình thực tập đầy bổ ích tại AWS Việt Nam, sinh viên đã củng cố vững chắc nền tảng kiến thức lý thuyết đã được giảng dạy tại nhà trường HUTECH, đồng thời mở rộng thêm nhiều kiến thức thực tế quý báu:
- Nắm vững kiến thức nền tảng về thiết kế phân mạng đám mây an toàn, định tuyến thông qua Internet Gateway, chia CIDR Block và cơ chế lọc traffic bằng Security Group và Network ACLs.
- Hiểu rõ triết lý thiết kế hệ thống Serverless không máy chủ (AWS Lambda), cách tối ưu hóa vòng đời container tính toán (MicroVMs) để tiết kiệm tài nguyên.
- Thấu hiểu sâu sắc các tiêu chuẩn bảo mật dữ liệu an toàn trên đám mây, cách viết IAM Policy phân quyền chặt chẽ theo nguyên lý Quyền tối thiểu (Least Privilege) giúp bảo mật tài sản thông tin cho các tổ chức doanh nghiệp.

### 4.2. Những kỹ năng thực hành đám mây đã học hỏi và làm chủ thành công
Bằng sự chỉ bảo hướng dẫn tận tình của các mentor tại AWS Việt Nam và cộng đồng First Cloud Journey, sinh viên đã tích lũy và làm chủ được các kỹ năng kỹ thuật thực chiến:
- Sử dụng thành thạo AWS CLI để kiểm soát hạ tầng thông qua dòng lệnh nhanh chóng và tự động hóa.
- Viết và triển khai thành công các kịch bản hạ tầng Terraform IaC phức tạp, giúp khởi dựng hàng loạt tài nguyên VPC, RDS, Lambda, API Gateway chỉ trong vài phút.
- Khắc phục thành công các lỗi thực tế nghiêm trọng như lỗi hệ thống tệp chỉ đọc (Read-only filesystem) của AWS Lambda bằng cách nghiên cứu tích hợp bộ SDK AWS S3 vào controller C# để lưu trữ tệp tin tĩnh tối ưu.
- Làm chủ kỹ năng sửa lỗi kết nối mạng chéo nguồn CORS giữa Vercel Hosting và API Gateway giúp hệ thống vận hành trơn tru trên internet.

### 4.3. Những kinh nghiệm thực tiễn và kỹ năng mềm đã tích lũy được (Tạo thiện cảm)
Đặc biệt, đợt thực tập này đã giúp sinh viên trưởng thành vượt bậc về tác phong làm việc chuyên nghiệp và các kỹ năng mềm quan trọng:
- Học hỏi được tác phong làm việc chủ động, đầy trách nhiệm dựa trên nguyên tắc "Ownership" và tinh thần liên tục học hỏi "Learn and Be Curious" từ môi trường làm việc toàn cầu của AWS.
- Trải nghiệm quy trình làm việc nhóm theo mô hình Agile hiện đại, tham gia các buổi họp review kỹ thuật giúp rèn luyện kỹ năng trình bày giải pháp, biết lắng nghe phản hồi xây dựng từ các mentor để hoàn thiện sản phẩm.
- Tích lũy kinh nghiệm quý báu về tư duy giải quyết vấn đề dưới áp lực thời gian (Troubleshooting & Debugging) và khả năng thích ứng linh hoạt trong môi trường công nghệ thay đổi nhanh chóng. Sinh viên cảm thấy vô cùng may mắn và tự hào khi được trải nghiệm môi trường văn hóa tôn trọng sự khác biệt và đầy tính chia sẻ tại AWS Việt Nam.

### 4.4. Chi tiết các kết quả công việc đã đóng góp cho cơ quan nơi thực tập và cộng đồng
Với tinh thần cống hiến và nỗ lực mang lại giá trị thiết thực, sinh viên đã hoàn thành xuất sắc các sản phẩm đóng góp:
- Xây dựng hoàn chỉnh giải pháp quản lý ký túc xá thông minh SmartDorm chạy ổn định end-to-end trên môi trường Cloud AWS với chi phí vận hành tối ưu gần như 0 USD/tháng, đóng vai trò là một case-study thực tế xuất sắc cho mô hình Serverless C# chạy trên Lambda.
- Biên soạn chi tiết tệp tài liệu hướng dẫn triển khai kỹ thuật (Workshop Lab) sạch sẽ, rõ ràng để lưu trữ làm tài liệu tham khảo cho các khóa học viên tiếp theo của chương trình First Cloud Journey (FCJ).
- Đóng góp bài viết kỹ thuật chia sẻ giải pháp khắc phục lỗi ghi đĩa của AWS Lambda và phương pháp tối ưu hóa chi phí đám mây RDS PostgreSQL để đăng tải lên diễn đàn cộng đồng, góp phần hỗ trợ tài liệu học tập bổ ích cho các bạn sinh viên HUTECH và cộng đồng kỹ sư Cloud trẻ tại Việt Nam.

---

## Danh mục báo cáo chi tiết (FCJ Requirements)

Vui lòng nhấp vào các liên kết dưới đây để truy cập chi tiết nội dung công việc và kết quả kỹ thuật của đợt thực tập tốt nghiệp:

1.  **[1. Nhật ký công việc hàng tuần (Worklog)](1-Worklog/)**
2.  **[2. Đề xuất dự án thực tập (Proposal)](2-Proposal/)**
3.  **[3. Các bài blogs đã dịch & biên soạn](3-BlogsTranslated/)**
4.  **[4. Các sự kiện đã tham gia](4-EventParticipated/)**
5.  **[5. Workshop Lab chính (Workshop)](5-Workshop/)**
6.  **[6. Tự đánh giá năng lực (Self-evaluation)](6-Self-evaluation/)**
7.  **[7. Đóng góp ý kiến (Feedback)](7-Feedback/)**
