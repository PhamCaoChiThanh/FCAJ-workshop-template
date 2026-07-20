---
title: "5.8 Cleanup Resources"
date: 2026-07-10
weight: 8
chapter: false
---

## 5.8 Infrastructure Cleanup to Prevent Charges

Once you have completed your testing or finished demonstrating this lab, it is crucial to teardown your active cloud resources. AWS charges for managed databases based on duration of operation, and leaving resources running idle will consume your Free Tier credits or incur costs.

Using Terraform, you can tear down the entire cloud stack using a single command.

---

### 1. Destroy Command Workflow

Navigate your terminal to the `terraform/` folder and execute:

```bash
terraform destroy -var="db_password=Chithanh123plus" -auto-approve
```

*Parameter Details:*
*   `-var="db_password=..."`: Supplies the sensitive database password variable to ensure proper authentication during resource deletion.
*   `-auto-approve`: Instructs Terraform to bypass the interactive confirmation prompt, executing the deletion sequence immediately.

---

### 2. Expected Resource Deletion Sequence

Upon invocation, Terraform maps resource dependencies in reverse order and systematically tears down the stack:

1.  De-associates route tables and terminates the Internet Gateway.
2.  Deletes the Amazon RDS PostgreSQL instance (this process takes about 3-5 minutes as AWS detaches active networking connections).
3.  Terminates AWS Lambda functions, S3 objects, Function URLs, and HTTP API Gateways.
4.  Removes IAM execution roles and policy attachments.
5.  Terminal prints the final success log:
    ```text
    Destroy complete! Resources: 18 destroyed.
    ```

---

> [!WARNING]
> **Data Loss Warning:**
> Running `terraform destroy` permanently erases all tables, relationships, and seed data housed within the Amazon RDS PostgreSQL database instance. Perform a database dump/backup prior to executing this command if you wish to retain test records.
