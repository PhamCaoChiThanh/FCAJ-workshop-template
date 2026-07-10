---
title: "Week 12: 13/07 - 19/07"
date: 2026-07-10
weight: 12
chapter: false
---

## Week 12: Amazon S3 File Upload Integration & Client-side URL Masking (13/07 - 19/07)

### Tasks Accomplished
*   Fixed write errors caused by AWS Lambda's read-only filesystem restrictions by refactoring upload endpoints with Amazon S3 SDK.
*   Configured IAM role policies allowing Lambda to execute `s3:PutObject` actions via Terraform.
*   Implemented Next.js URL Masking, converting administrative page URLs to hashed parameters like `/?sig=xxx&id=yyy` for enhanced client-side route security.
*   Deployed frontend to Vercel and performed complete validation of the application.

### Outcomes
*   Full system deployed successfully to production on AWS Cloud with monthly operating costs near $0.
