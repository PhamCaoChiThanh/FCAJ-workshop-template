---
title: "Blog 2: Lambda Read-Only Fix"
date: 2026-07-10
weight: 2
chapter: false
---
## Solving AWS Lambda's Read-Only Filesystem with Amazon S3
When running ASP.NET Core on AWS Lambda, local file uploads fail because the filesystem is read-only. This blog covers configuring the Amazon S3 SDK inside C# and provisioning an S3 bucket via Terraform to handle avatar and national ID uploads securely.
