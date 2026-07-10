---
title: "Blog 1: Infrastructure Cost Optimization"
date: 2026-07-10
weight: 1
chapter: false
---

## Journey of Optimizing AWS Infrastructure Cost for SmartDorm to Almost $0

### Introduction
During the development of SmartDorm, our key goal was to ensure the system is stable, secure, and fast, but with minimal hosting bills (approaching $0 during staging).

Here are the practical methods we applied:

#### 1. Eliminating Idle Server Costs with AWS Lambda (Serverless)
*   **Problem:** Renting traditional EC2 instances incurs 24/7 costs even when there are no users during nighttime.
*   **Solution:** Deployed C# Backend to AWS Lambda. The system only runs and incurs costs on-demand (per millisecond), leveraging AWS Free Tier's 1 million free requests/month.

#### 2. Leveraging HTTP API Gateway instead of REST API Gateway
*   HTTP API (API Gateway v2) reduces costs by up to 70% compared to traditional REST API Gateway, while achieving much lower latency.

#### 3. Removing NAT Gateway (Saving ~$32/month)
*   **Problem:** To connect Lambda safely to RDS PostgreSQL in a private subnet, AWS typically recommends a NAT Gateway, which is costly.
*   **Solution:** Configured intelligent routing and Security Groups directly between Lambda and RDS, eliminating NAT Gateway entirely.

#### 4. Utilizing RDS PostgreSQL running on ARM chip (db.t4g.micro)
*   Leveraged AWS RDS Free Tier's 750 hours/month running on Graviton2 (ARM) processors for highly efficient, cost-free database performance.
