---
title: "5.1 Project Overview"
date: 2026-07-10
weight: 1
chapter: false
---
## 5.1 Overview & Architecture

### 1. Business & Technical Context

In student housing (dormitories) and rental management, maintaining a 24/7 online system is critical for students to register rooms, report maintenance issues, and manage/pay monthly utility invoices.

However, historical traffic data shows that systems experience high idle rates during off-peak hours (e.g., late nights or breaks). Maintaining a traditional virtual server (like EC2/VPS) 24/7 leads to substantial cost overheads (typically $15 - $40/month per instance).

**Secure SmartDorm** resolves this cost inefficiency by adopting an event-driven **Serverless** architecture on AWS, reducing the monthly base operational cost to **nearly $0/month** in staging and testing phases by leveraging the AWS Free Tier.

### 2. Detailed Staging Network Architecture

The diagram below illustrates the network layout and data lifecycle:

![1783657347600](image/_index/1783657347600.png)

### 3. Service Interactions & Components:

*   **Frontend (Next.js / Vercel):** The user interface is developed with Next.js 16 and TailwindCSS, hosted as static exports on Vercel Cloud Hosting. All CRUD operations are routed securely via HTTPS endpoints to the backend.
*   **API Gateway & Lambda Function URL:** Serves as the secure public gateway (API endpoint) for the backend. Utilizing direct Function URLs or HTTP API Gateways minimizes overhead costs, permitting millions of free requests per month.
*   **AWS Lambda (ASP.NET Core / .NET 10 API):** The serverless compute engine. Upon receiving an API request, AWS Lambda instantiates a micro-container containing the compiled C# binary to process requests, spinning down to zero when idle.
*   **Amazon RDS (PostgreSQL):** A managed relational database deployed within a private subnet. RDS PostgreSQL stores structured information (rooms, tenants, contracts, invoices). It is configured to restrict public traffic, allowing traffic only from the Lambda function security group boundary.
*   **Amazon S3 (Simple Storage Service):** Since AWS Lambda operates on a read-only filesystem for security, S3 serves as the persistent storage layer for student avatars, ID scans, and invoice PDF documents.
*   **Amazon Bedrock (Generative AI):** Integrated to automate the debt reminder process. Upon identifying overdue invoices, the system queries the Bedrock API to generate personalized debt reminder emails based on the length of delinquency, which are subsequently dispatched to tenants.
