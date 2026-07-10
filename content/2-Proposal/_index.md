---
title: "Project Proposal"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 2. </b> "
---

## Project Proposal: SmartDorm - Smart Dormitory Management System

### 1. Project Overview
SmartDorm is a comprehensive digitization solution for managing dormitories and large student housing units, breaking administrative barriers between landlords/admins and student tenants.

### 2. Objectives
*   **Process Digitization:** Transition from paper-based contracts and manual billing to automated online workflows.
*   **Utility & Monitoring Efficiency:** Visualize monthly electricity/water logs, reducing data discrepancies.
*   **Infrastructure Cost Optimization:** Attain near-zero hosting costs (~$0/month during staging phase).

### 3. Problem Statement
*   Time-consuming manual record keeping and human error in recording utility indexes.
*   Delays in payments and student notification delivery.
*   Expensive idle costs of traditional virtual servers (EC2/VPS) running 24/7.

### 4. Solution Architecture (AWS Cloud)
The system leverages cost-efficient AWS services:
*   **API Gateway (HTTP API):** Low-cost, fast API routing proxy.
*   **AWS Lambda:** Serverless business logic execution (C# .NET Core), billed per millisecond.
*   **Amazon S3:** Static asset storage for frontend and uploaded user profiles/IDs.
*   **Amazon RDS (PostgreSQL):** Managed relational database instance running on Graviton2 processor for optimized price-to-performance.

### 5. Project Timeline (12 Weeks)
*   **Weeks 1-4:** DB design, core room management, and booking APIs implementation.
*   **Weeks 5-8:** Utility indexing algorithms, billing systems, Next.js UI integration, and local testing.
*   **Weeks 9-12:** Terraform IaC scripting, deploying AWS RDS, Lambda Serverless, S3 integration, and Vercel hosting.

### 6. Risks & Mitigation
*   *Lambda Filesystem Constraints:* Resolved by integrating Amazon S3 for direct file uploads.
*   *CORS Policies:* Configured precise API Gateway CORS headers to secure endpoints while enabling Vercel client operations.
