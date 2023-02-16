# S3 Bucket does not have logging enabled

In the code example S3 Bucket does not have logging enabled

```terraform
resource "aws_s3_bucket" "insecure_example" {

}

```

# Why it's vulnerable?

There is no way to determine the access to this bucket

# How to fix?

Add a logging block to the resource to enable access logging

```terraform
resource "aws_s3_bucket" "secure_example" {
    logging {
        target_bucket = "target-bucket"
    }
}
```
