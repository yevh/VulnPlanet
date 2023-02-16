# Log group is not encrypted

In the code example Log group is not encrypted

```terraform
 resource "aws_cloudwatch_log_group" "insecure_example" {
    name = "insecure_example"

 }
```

# Why it's vulnerable?

CloudWatch log groups are encrypted by default, however, to get the full benefit of controlling key rotation and other KMS aspects a KMS CMK should be used. Log data may be leaked if the logs are compromised. No auditing of who have viewed the logs.

# How to fix?

Enable CMK encryption of CloudWatch Log Groups

```terraform
 resource "aws_cloudwatch_log_group" "secure_example" {
    name = "secure_example"

    kms_key_id = aws_kms_key.log_key.arn
 }
```
