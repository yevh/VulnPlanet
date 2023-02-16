# Load balancer is not drop invalid headers

The code example is not set up to drop invalid headers

```terraform
 resource "aws_alb" "insecure_example" {
    name               = "bad_alb"
    internal           = false
    load_balancer_type = "application"

    access_logs {
      bucket  = aws_s3_bucket.lb_logs.bucket
      prefix  = "test-lb"
      enabled = true
    }

    drop_invalid_header_fields = false
   }

```

# Why it's vulnerable?
Invalid headers being passed through to the target of the load balance may allow to exploit vulnerabilities

# How to fix?

Set drop_invalid_header_fields to true

```terraform
 resource "aws_alb" "secure_example" {
    name               = "good_alb"
    internal           = false
    load_balancer_type = "application"

    access_logs {
      bucket  = aws_s3_bucket.lb_logs.bucket
      prefix  = "test-lb"
      enabled = true
    }

    drop_invalid_header_fields = true
   }
```
