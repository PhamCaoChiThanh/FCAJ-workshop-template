---
title: "5.3 Provisioning with Terraform"
date: 2026-07-10
weight: 3
chapter: false
---

## 5.3 Cloud Automation with Terraform IaC

To manage cloud resources systematically, ensure repeatability, and speed up deployments, the SmartDorm project utilizes **Infrastructure as Code (IaC)** through **Terraform**.

Here is the setup for provisioning the Virtual Private Cloud (VPC), public/private subnets, security boundaries, and an Amazon RDS PostgreSQL database instance.

### 1. Provider Configuration (`provider.tf`)
This file instructs Terraform to download the AWS provider and set Singapore (`ap-southeast-1`) as the default region to ensure low latency for Vietnam-based clients:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

### 2. Main Configuration (`main.tf`)
This file defines all network structures (VPC, subnets, route tables, internet gateways) and the managed database instance:

```hcl
# 1. INDEPENDENT VIRTUAL PRIVATE CLOUD (VPC)
resource "aws_vpc" "smartdorm_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  tags = {
    Name = "smartdorm-vpc"
  }
}

# 2. INTERNET GATEWAY
resource "aws_internet_gateway" "smartdorm_igw" {
  vpc_id = aws_vpc.smartdorm_vpc.id
  tags = {
    Name = "smartdorm-igw"
  }
}

# 3. SUBNET PARTITIONING
# Public Subnet A - Accessible components (e.g., AWS Lambda function)
resource "aws_subnet" "smartdorm_subnet_a" {
  vpc_id                  = aws_vpc.smartdorm_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "${var.aws_region}a"
  map_public_ip_on_launch = true
  tags = {
    Name = "smartdorm-subnet-a"
  }
}

# Private Subnet B - Relational Database Service (RDS) to isolate from direct Internet traffic
resource "aws_subnet" "smartdorm_subnet_b" {
  vpc_id                  = aws_vpc.smartdorm_vpc.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "${var.aws_region}b"
  map_public_ip_on_launch = true
  tags = {
    Name = "smartdorm-subnet-b"
  }
}

# 4. ROUTE TABLE & NETWORK ROUTING
resource "aws_route_table" "smartdorm_rt" {
  vpc_id = aws_vpc.smartdorm_vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.smartdorm_igw.id
  }
  tags = {
    Name = "smartdorm-rt"
  }
}

resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.smartdorm_subnet_a.id
  route_table_id = aws_route_table.smartdorm_rt.id
}

resource "aws_route_table_association" "b" {
  subnet_id      = aws_subnet.smartdorm_subnet_b.id
  route_table_id = aws_route_table.smartdorm_rt.id
}

# 5. DATABASE SECURITY GROUP
resource "aws_security_group" "rds_sg" {
  name        = "smartdorm_rds_sg"
  description = "Allow PostgreSQL incoming connections"
  vpc_id      = aws_vpc.smartdorm_vpc.id

  # Allow port 5432 ingress for local database administration/seeding
  ingress {
    description = "PostgreSQL"
    from_port   = 5432
    to_port     = 5432
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# 6. AMAZON RDS POSTGRESQL INSTANCE
resource "aws_db_subnet_group" "smartdorm_db_subnet_group" {
  name       = "smartdorm-db-subnet-group"
  subnet_ids = [aws_subnet.smartdorm_subnet_a.id, aws_subnet.smartdorm_subnet_b.id]
  tags = {
    Name = "smartdorm-db-subnet-group"
  }
}

resource "aws_db_instance" "smartdorm_db" {
  identifier             = "smartdorm-postgres-db"
  allocated_storage      = 20
  storage_type           = "gp2"
  engine                 = "postgres"
  engine_version         = "15"
  instance_class         = "db.t4g.micro" # Free Tier eligible ARM instance class
  username               = var.db_username
  password               = var.db_password
  parameter_group_name   = "default.postgres15"
  skip_final_snapshot    = true
  publicly_accessible    = true # Enabled for easier verification and migrations
  vpc_security_group_ids = [aws_security_group.rds_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.smartdorm_db_subnet_group.name
}
```

### 3. Variables Definition (`variables.tf`)
Variables ensure sensitive credentials remain uncommitted to code repositories:

```hcl
variable "aws_region" {
  type    = string
  default = "ap-southeast-1"
}

variable "db_username" {
  type    = string
  default = "postgres"
}

variable "db_password" {
  type      = string
  sensitive = true
}
```

### 4. Provisioning Command Workflow

Navigate your terminal to the `terraform/` directory and execute the following commands:

*   **Step 1: Initialize working directory and download AWS provider modules:**
    ```bash
    terraform init
    ```
*   **Step 2: Dry-run/Preview resources before execution:**
    ```bash
    terraform plan -var="db_password=Chithanh123plus"
    ```
*   **Step 3: Provision resources on AWS Cloud:**
    ```bash
    terraform apply -var="db_password=Chithanh123plus" -auto-approve
    ```
    *(PostgreSQL RDS provisioning takes approximately 3-5 minutes. Wait until `Apply complete!` displays).*
