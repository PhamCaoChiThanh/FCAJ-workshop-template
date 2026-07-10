---
title: "5.1 Project Overview"
date: 2026-07-10
weight: 1
chapter: false
---

## 5.1 Overview & Architecture

### 1. Business & Technical Context
Maintaining dedicated cloud servers (like EC2/VPS) 24/7 for student housing software wastes significant funds due to high idle time during non-peak hours (e.g., late nights). 

**SmartDorm** solves this inefficiency by adopting an event-driven **Serverless** architecture on AWS, reducing the monthly operational cost to **nearly $0/month** in staging.

### 2. Detailed Staging Network Architecture
The diagram below illustrates the network layout and data lifecycle:

```text
+-------------------------------------------------------------------------------+
|                             AWS CLOUD (Region: ap-southeast-1)                |
|                                                                               |
|   +-----------------------------------------------------------------------+   |
|   | VPC (Isolated Virtual Private Cloud - CIDR: 10.0.0.0/16)              |   |
|   |                                                                       |   |
|   |  +-----------------------------------------------------------------+  |   |
|   |  | Subnet A (Public Subnet - CIDR: 10.0.1.0/24)                    |  |   |
|   |  |                                                                 |  |   |
|   |  |  [API Gateway] (HTTP API Proxy v2)                              |  |   |
|   |  |        |                                                        |  |   |
|   |  |        v (API Proxy Redirect)                                   |  |   |
|   |  |  [AWS Lambda] (C# .NET 10 Serverless Backend)                   |  |   |
|   |  |        | (Secure connection on Port 5432)                       |  |   |
|   |  +--------|--------------------------------------------------------+  |   |
|   |           |                                                           |   |
|   |  +--------v--------------------------------------------------------+  |   |
|   |  | Subnet B (Private Subnet - CIDR: 10.0.2.0/24)                    |  |   |
|   |  |                                                                 |  |   |
|   |  |  [Amazon RDS PostgreSQL] (db.t4g.micro - Graviton2 ARM)         |  |   |
|   |  +-----------------------------------------------------------------+  |   |
|   +-----------------------------------------------------------------------+   |
|                                                                               |
|   +-----------------------+               +-------------------------------+   |
|   | Amazon S3 (Bucket)    |               | Vercel Cloud Hosting          |   |
|   | (Static Media assets) |               | (Static Frontend Next.js)     |   |
|   +-----------------------+               +-------------------------------+   |
+-------------------------------------------------------------------------------+
```

### 3. Service Interactions
*   **Vercel:** Hosts Next.js client builds with free HTTPS/SSL certificates.
*   **HTTP API Gateway:** Proxies incoming HTTPS requests to the Lambda function. Billed at 70% cheaper than REST API.
*   **AWS Lambda:** Processes backend operations serverless. Instantiates micro-VM containers on-demand, billed per millisecond of compute.
*   **Amazon S3:** Hosts permanent public file uploads (avatars, ID scans) bypassing Lambda's read-only disk layout.
*   **Amazon RDS:** Managed PostgreSQL instance deployed in a secure private subnet, reachable only from the Lambda security group boundary.
