---
title: "5.2 Prerequisites"
date: 2026-07-10
weight: 2
chapter: false
---

## 5.2 Prerequisites

Before proceeding with this workshop, ensure that your local machine is equipped with the following runtimes, SDKs, and CLI utilities:

### 1. AWS Account Configuration
*   **Sign Up:** Create an AWS account at [https://aws.amazon.com/](https://aws.amazon.com/) to utilize the **AWS Free Tier** (12 months free).
*   **Access Credentials:** Generate an IAM User with administrative rights (`AdministratorAccess`) and export the **Access Key ID** and **Secret Access Key** for authentication.

### 2. Command Line Interface (CLI) Tools

*   **AWS CLI v2:** Used for managing AWS cloud services via terminal commands.
    *   *Installation:* Download and install the CLI from [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
    *   *Configuration:* Run the following command in terminal/PowerShell:
        ```bash
        aws configure
        ```
        *Input parameters:*
        - `AWS Access Key ID`: [Your IAM Access Key ID]
        - `AWS Secret Access Key`: [Your IAM Secret Access Key]
        - `Default region name`: `ap-southeast-1` (Singapore)
        - `Default output format`: `json`
    *   *Verification:* Run `aws sts get-caller-identity` to confirm connection.

*   **Terraform CLI (Version >= 1.5.0):** An Infrastructure as Code (IaC) tool for provisioning cloud infrastructure.
    *   *Installation:* Obtain the executable binary from [Terraform Downloads](https://developer.hashicorp.com/terraform/downloads) and add it to your system's PATH.
    *   *Verification:* Check installation using:
        ```bash
        terraform version
        ```

### 3. Application Runtimes & SDKs

*   **.NET SDK 10.0:** Microsoft's C# software development kit, necessary for compiling the backend Web API.
    *   *Installation:* Retrieve the installer from [Download .NET 10.0](https://dotnet.microsoft.com/download/dotnet/10.0).
    *   *Verification:* Execute the command:
        ```bash
        dotnet --version
        ```

*   **Node.js (Version >= 18.0.0):** JavaScript runtime environment required for building and exporting the Next.js Frontend.
    *   *Installation:* Download the LTS release from [Node.js Official Website](https://nodejs.org/).
    *   *Verification:* Run:
        ```bash
        node -v
        npm -v
        ```

*   **Docker Desktop (Recommended):** Used for running local PostgreSQL instances containerized, bypassing complex manual database installations on host machines.
    *   *Installation:* Get Docker from [Docker Desktop Installation](https://www.docker.com/products/docker-desktop/).
