---
title: "5.5 Deploy Backend"
date: 2026-07-10
weight: 5
chapter: false
---

## 5.5 Compiling & Deploying C# Backend on AWS Lambda

AWS Lambda provides an optimal serverless compute model, scaling automatically with traffic and generating zero charges when idle. Since AWS Lambda does not natively ship a managed runtime for .NET 10 yet, we compile the C# Web API as a self-contained application and run it inside a **Custom Runtime** on Amazon Linux.

---

### 1. Application Compilation & Packaging

To run on AWS Lambda's Linux x64 platform, the C# Web API must be compiled in Release mode, contain its own .NET runtime dependencies, and have its entry point executable renamed to `bootstrap`.

Open your terminal or PowerShell at the `backend/` directory and execute the packaging workflow:

```powershell
# Step A: Clean up any previous publish builds
Remove-Item -Path "publish" -Recurse -Force -ErrorAction SilentlyContinue

# Step B: Publish as self-contained targeting the Linux 64-bit platform
dotnet publish -c Release -r linux-x64 --self-contained true -o publish /p:GenerateRuntimeConfigurationFiles=true

# Step C: Rename the primary Web API executable to "bootstrap"
# This name is mandatory for AWS Lambda Custom Runtimes to boot up successfully
Rename-Item -Path "publish/SmartDorm.Api" -NewName "bootstrap"

# Step D: Compress the contents of the publish folder into backend.zip
Compress-Archive -Path "publish/*" -DestinationPath "../backend.zip" -Force
```

---

### 2. AWS Lambda Definition in Terraform

Add the Lambda configuration to your Terraform files to upload the `backend.zip` bundle, instantiate the Lambda function, and inject essential database credentials and settings as environment variables:

```hcl
# 1. S3 BUCKET FOR DEPLOYMENT ARTIFACTS
resource "aws_s3_bucket" "lambda_deploy_bucket" {
  bucket_prefix = "smartdorm-lambda-deploy-"
  force_destroy = true
}

resource "aws_s3_object" "lambda_code" {
  bucket      = aws_s3_bucket.lambda_deploy_bucket.id
  key         = "backend.zip"
  source      = "../backend.zip"
  source_hash = filebase64sha256("../backend.zip")
}

# 2. LAMBDA FUNCTION SPECIFICATION
resource "aws_lambda_function" "backend_api" {
  function_name    = "SmartDormBackendAPI"
  role             = aws_iam_role.lambda_exec_role.arn
  handler          = "bootstrap" # Custom runtime executable target
  runtime          = "provided.al2023" # Amazon Linux 2023 Custom Runtime
  s3_bucket        = aws_s3_bucket.lambda_deploy_bucket.id
  s3_key           = aws_s3_object.lambda_code.key
  source_code_hash = filebase64sha256("../backend.zip")

  timeout     = 30  # Max request duration in seconds
  memory_size = 512 # Allocated memory in MB (balances CPU allocation to decrease cold starts)

  environment {
    variables = {
      # Dynamic connection string using RDS postgres instance endpoint address
      ConnectionStrings__DefaultConnection = "Host=${aws_db_instance.smartdorm_db.address};Database=smartdorm;Username=${aws_db_instance.smartdorm_db.username};Password=${var.db_password};Port=5432"
      Jwt__Secret                          = var.jwt_secret
      AWS__Region                          = var.aws_region
      AWS__BucketName                      = aws_s3_bucket.frontend_bucket.id
      DOTNET_SYSTEM_GLOBALIZATION_INVARIANT = "true" # Trims binary size and circumvents region cultures
    }
  }
}
```

---

### 3. Exposing the API: Lambda Function URL vs API Gateway

To enable the client frontend to access the server backend over the web, we must establish public HTTPS entry points. Terraform registers both methods for versatility:

*   **Method 1: Lambda Function URL (Lowest latency & cost)**
    *   Exposes a direct HTTP(S) endpoint to invoke the Lambda function without intermediate routing hops.
    ```hcl
    resource "aws_lambda_function_url" "api_endpoint" {
      function_name      = aws_lambda_function.backend_api.function_name
      authorization_type = "NONE" # Public endpoints
      cors {
        allow_origins     = ["*"]
        allow_methods     = ["*"]
        allow_headers     = ["date", "keep-alive", "content-type", "authorization"]
        max_age           = 86400
      }
    }
    ```

*   **Method 2: HTTP API Gateway (Robust API management)**
    *   Enables standard routing proxies, throttling limits, and enhanced CORS policies. A `{proxy+}` routing rule catches and forwards all traffic to Lambda.
