# Sơ đồ Kiến trúc AWS Hỗ trợ Phân vùng & Dự phòng Database (Sản xuất)

Khi hệ thống đã đi vào hoạt động ổn định và có lượng người dùng thực tế, việc thiết lập **Phân vùng mạng đa vùng sẵn sàng (Multi-AZ)** và tách biệt luồng Đọc/Ghi (Read/Write Replicas) cho database là cực kỳ quan trọng để đảm bảo tính chịu lỗi (Fault Tolerance) và hiệu năng tải.

### Mã nguồn Mermaid (Nhập vào draw.io):
Copy đoạn mã dưới đây và dán vào draw.io qua menu **Sắp xếp** -> **Chèn** -> **Nâng cao** -> **Mermaid...**:

```mermaid
graph TB
    subgraph Client["Trình duyệt Người dùng"]
        Browser["Người dùng cuối"]
    end

    subgraph Vercel["Vercel Cloud Hosting"]
        Frontend["Frontend Static"]
    end

    subgraph AWS["AWS Cloud (ap-southeast-1)"]
        S3["Amazon S3 (Bucket)"]
        
        subgraph VPC["VPC (10.0.0.0/16)"]
            
            subgraph AZ1["Availability Zone 1 (ap-southeast-1a)"]
                subgraph PubSub1["Subnet Công khai A1"]
                    APIGW["API Gateway"]
                end
                
                subgraph PriAppSub1["Subnet Ứng dụng B1"]
                    Lambda1["AWS Lambda (Writer App)"]
                end
                
                subgraph PriDBSub1["Subnet Database C1"]
                    RDS_Primary["Amazon RDS PostgreSQL<br/>(Primary - Ghi chính)"]
                end
            end
            
            subgraph AZ2["Availability Zone 2 (ap-southeast-1b)"]
                subgraph PriAppSub2["Subnet Ứng dụng B2"]
                    Lambda2["AWS Lambda (Reader App)"]
                end
                
                subgraph PriDBSub2["Subnet Database C2"]
                    RDS_Standby["Amazon RDS PostgreSQL<br/>(Standby - Dự phòng sự cố)"]
                end
                
                subgraph PriDBSub3["Subnet Database C3"]
                    RDS_Replica["Amazon RDS PostgreSQL<br/>(Read Replica - Chỉ đọc)"]
                end
            end

            VPCEndpoint["VPC Endpoint (S3)"]
        end
    end

    %% Luồng kết nối & Truy cập
    Browser --> Frontend
    Browser -->|Gọi API| APIGW
    Browser -->|Tải ảnh| S3
    
    APIGW -->|Định tuyến tải| Lambda1
    APIGW -->|Định tuyến tải| Lambda2

    %% Luồng ghi & đọc từ Database
    Lambda1 -->|1. Chỉ ghi (Write Endpoint)| RDS_Primary
    Lambda2 -->|2. Chỉ đọc (Reader Endpoint)| RDS_Replica

    %% Đồng bộ dữ liệu Database
    RDS_Primary -.->|Đồng bộ tức thời (Synchronous)| RDS_Standby
    RDS_Primary -.->|Đồng bộ bất đối xứng (Asynchronous)| RDS_Replica

    %% VPC endpoint
    Lambda1 & Lambda2 --> VPCEndpoint
    VPCEndpoint <--> S3

    %% Định nghĩa kiểu dáng
    classDef write fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef read fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef standby fill:#eceff1,stroke:#37474f,stroke-width:2px,stroke-dasharray: 5 5;
    
    class RDS_Primary write;
    class RDS_Replica read;
    class RDS_Standby standby;
```
