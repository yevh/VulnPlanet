# S3 Access block should block public policy

In the code example users could put a policy that allows public access

```terraform
resource "aws_s3_bucket" "example" {
  bucket = "mybucket"
}

resource "aws_s3_bucket_public_access_block" "insecure_example" {
  bucket = aws_s3_bucket.example.id
}

resource "aws_s3_bucket_public_access_block" "insecure_example" {
  bucket = aws_s3_bucket.example.id 
  block_public_policy = false
}
```

# Why it's vulnerable?
S3 bucket policy should have block public policy to prevent users from putting a policy that enable public access

# How to fix?

Prevent policies that allow public access being PUT

```terraform
resource "aws_s3_bucket" "example" {
  bucket = "mybucket"
}

resource "aws_s3_bucket_public_access_block" "secure_example" {
  bucket = aws_s3_bucket.example.id 
  block_public_policy = true 
}
```
