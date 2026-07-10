---
title: "Week 2: 24/04 - 02/05"
date: 2026-07-10
weight: 2
chapter: false
---

## Week 2: Deep Dive into VPC, Amazon S3, and IAM Security

### Objectives:
* Master Amazon Virtual Private Cloud (VPC) network planning and implementation.
* Explore Amazon S3 object storage capabilities, CORS rules, and IAM access controls.

### Tasks to Deploy This Week:

| Day | Task | Start Date | End Date | References |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------------ | --------------------------------------------------------------------------------- |
| Mon | - Study AWS VPC components:<br>&emsp; + CIDR allocations & IP Subnetting<br>&emsp; + Public Subnets and Private Subnets<br>&emsp; + Internet Gateways & Route Tables<br>&emsp; + Security Groups vs Network ACLs (NACLs) | 24/04/2026 | 24/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| Tue | - Research Amazon S3:<br>&emsp; + Buckets, Objects, and Storage Classes<br>&emsp; + S3 Bucket Policies (JSON)<br>&emsp; + Static Website Hosting | 25/04/2026 | 25/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| Wed | - Research AWS Identity & Access Management (IAM):<br>&emsp; + Users, Groups, and Roles<br>&emsp; + Custom JSON Policies<br>&emsp; + Principle of Least Privilege enforcement | 26/04/2026 | 26/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| Thu | - **Hands-on networking:**<br>&emsp; + Build a custom VPC with CIDR `10.0.0.0/16`<br>&emsp; + Provision 1 Public and 1 Private Subnet<br>&emsp; + Route public traffic through an Internet Gateway (IGW)<br>&emsp; + Validate internet routing on a test EC2 instance. | 27/04/2026 | 28/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |
| Fri | - **Hands-on storage:**<br>&emsp; + Create an S3 Bucket and upload static HTML assets.<br>&emsp; + Enable static website hosting.<br>&emsp; + Write a custom JSON Bucket Policy to permit public-read access. | 29/04/2026 | 29/04/2026 | [AWS Study Group Docs](https://cloudjourney.awsstudygroup.com/) |

### Outcomes:
*   **Conceptual:** Understood network isolation principles to protect database tiers.
*   **Practical:**
    *   Designed and tested a custom VPC with proper subnet routing.
    *   Wrote custom JSON policies for Amazon S3 buckets, avoiding `403 Forbidden` errors.
    *   Configured S3 static website hosting and resolved cross-origin resources sharing (CORS) rules.
    *   Created and tested IAM roles for secure cross-service access.
