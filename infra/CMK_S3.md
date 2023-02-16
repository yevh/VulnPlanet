# S3 encryption should use Customer Managed Keys

In the code example AWS managed keys does not allow for fine grained control

```terraform
resource "aws_s3_bucket" "insecure_exampl" {
   bucket = "mybucket"

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm     = "AES256"
      }
    }
  }
}
```

# Why it's vulnerable?
Encryption using AWS keys provides protection for your S3 buckets. To increase control of the encryption and manage factors like rotation use customer managed keys.

# How to fix?

Enable encryption using customer managed keys

```terraform
resource "aws_kms_key" "secure_example" {
  enable_key_rotation = true
}

resource "aws_s3_bucket" "secure_example" {
   bucket = "mybucket"

   server_side_encryption_configuration {
     rule {
       apply_server_side_encryption_by_default {
         kms_master_key_id = aws_kms_key.example.arn
         sse_algorithm     = "aws:kms"
       }
     }
   }
 }
```
