# Unencrypted S3 bucket

In the code example bucket objects could be read if compromised

```terraform
 resource "aws_s3_bucket" "insecure_example" {
   bucket = "mybucket"
 }
```

# Why it's vulnerable?
S3 Buckets should be encrypted to protect the data that is stored within them if access is compromised

# How to fix?

Configure bucket encryption

```terraform
 resource "aws_s3_bucket" "secure_example" {
   bucket = "mybucket"

   server_side_encryption_configuration {
     rule {
       apply_server_side_encryption_by_default {
         kms_master_key_id = "arn"
         sse_algorithm     = "aws:kms"
       }
     }
   }
 }
```
