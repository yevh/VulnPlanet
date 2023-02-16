# S3 Access block should block public ACL

In the code example PUT calls with public ACLs specified can make objects public

```terraform
resource "aws_s3_bucket" "insecure_example" {
  bucket = "mybucket"
}

resource "aws_s3_bucket_public_access_block" "insecure_example" {
  bucket = aws_s3_bucket.insecure_example.id
}
```

# Why it's vulnerable?

S3 buckets should block public ACLs on buckets and any objects they contain. By blocking, PUTs with fail if the object has any public ACL a.

# How to fix?

Enable blocking any PUT calls with a public ACL specified

```terraform
resource "aws_s3_bucket" "secure_example" {
  bucket = "mybucket"
}

resource "aws_s3_bucket_public_access_block" "secure_example" {
  bucket = aws_s3_bucket.secure_example.id
  block_public_acls = true
}
```
