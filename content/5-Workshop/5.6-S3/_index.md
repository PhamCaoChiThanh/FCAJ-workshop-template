---
title: "5.6 S3 Integration"
date: 2026-07-10
weight: 6
chapter: false
---

## 5.6 Amazon S3 Storage & Upload Integration

AWS Lambda runs on a read-only filesystem to safeguard runtime environments, with the exception of the transient `/tmp` directory, which is size-restricted and automatically wiped clean once container execution concludes. Consequently, writing persistent file uploads (such as student avatars, government ID scans, or lease PDF documents) directly to local Web Server directories is not feasible.

To address this, the SmartDorm system integrates **Amazon S3 (Simple Storage Service)** to serve as a persistent, durable, and highly available object store for static assets.

---

### 1. IAM Policy Configuration for S3 Access

By default, AWS enforces a strict Zero Trust model (Least Privilege). Your Lambda function will throw an Access Denied error if it attempts to write to an S3 bucket without proper IAM permissions.

We must append an IAM Policy definition within the Lambda execution role resource block in Terraform to grant write and read access to our designated S3 Bucket:

```hcl
resource "aws_iam_role_policy" "lambda_s3_policy" {
  name = "smartdorm_lambda_s3_policy"
  role = aws_iam_role.lambda_exec_role.id

  # Authorize S3 write (PutObject), public permission tagging (PutObjectAcl), and read (GetObject)
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:PutObject",
          "s3:PutObjectAcl",
          "s3:GetObject"
        ]
        Resource = [
          # Restrict write capabilities strictly inside the designated frontend bucket namespace
          "${aws_s3_bucket.frontend_bucket.arn}/*"
        ]
      }
    ]
  })
}
```

---

### 2. File Upload API Controller Logic (`UploadController.cs`)

We build a dual-environment upload mechanism inside [UploadController.cs](file:///m:/SmartDorm/backend/Controllers/UploadController.cs):
*   **Local Development Environment:** If no bucket name environment variable is detected, file uploads fallback to saving locally under the host's `wwwroot/uploads/` directory.
*   **Production Environment (AWS Lambda):** The application detects the `AWS__BucketName` variable and initializes the Amazon S3 Client SDK to direct the binary stream straight into the S3 bucket.

Here is the C# controller implementation demonstrating this design:

```csharp
using Amazon.S3;
using Amazon.S3.Model;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System;
using System.IO;
using System.Threading.Tasks;

namespace SmartDorm.Api.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class UploadController : ControllerBase
    {
        [HttpPost]
        [DisableRequestSizeLimit] // Bypasses file size request limits for heavy uploads
        public async Task<IActionResult> UploadFile(IFormFile file)
        {
            if (file == null || file.Length == 0)
                return BadRequest(new { success = false, message = "No file data received" });

            // Read the target S3 bucket name from Lambda environment configuration variables
            var bucketName = Environment.GetEnvironmentVariable("AWS__BucketName");

            if (!string.IsNullOrEmpty(bucketName))
            {
                try
                {
                    // Stream upload directly to Amazon S3 via the official AWS SDK
                    var s3Client = new AmazonS3Client();
                    var fileKey = $"uploads/{Guid.NewGuid()}_{file.FileName}";

                    using (var stream = file.OpenReadStream())
                    {
                        var putRequest = new PutObjectRequest
                        {
                            BucketName = bucketName,
                            Key = fileKey,
                            InputStream = stream,
                            ContentType = file.ContentType
                        };
                        
                        await s3Client.PutObjectAsync(putRequest);
                    }

                    // Return the public HTTPS URL for the client application to reference
                    var publicUrl = $"https://{bucketName}.s3.amazonaws.com/{fileKey}";
                    return Ok(new { success = true, url = publicUrl });
                }
                catch (Exception ex)
                {
                    return StatusCode(500, new { success = false, message = "S3 Upload failed", error = ex.Message });
                }
            }

            // LOCAL DEVELOPMENT FALLBACK MECHANISM
            var uploadDir = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/uploads");
            if (!Directory.Exists(uploadDir)) 
                Directory.CreateDirectory(uploadDir);

            var fileName = $"{Guid.NewGuid()}_{file.FileName}";
            var filePath = Path.Combine(uploadDir, fileName);

            using (var stream = new FileStream(filePath, FileMode.Create))
            {
                await file.CopyToAsync(stream);
            }

            var localUrl = $"/uploads/{fileName}";
            return Ok(new { success = true, url = localUrl });
        }
    }
}
```
