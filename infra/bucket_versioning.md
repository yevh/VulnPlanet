# Bucket does not have versioning enabled

In the code example S3 bucket does not have versioning enabled

```terraform
resource "aws_s3_bucket" "insecure_example" {

}
```

# Why it's vulnerable?

Versioning in Amazon S3 is a means of keeping multiple variants of an object in the same bucket. You can use the S3 Versioning feature to preserve, retrieve, and restore every version of every object stored in your buckets. With versioning you can recover more easily from both unintended user actions and application failures. Deleted or modified data would not be recoverable.

# How to fix?

Enable versioning to protect against accidental/malicious removal or modification

```terraform
resource "aws_s3_bucket" "secure_example" {

    versioning {
        enabled = true
    }
}
```
