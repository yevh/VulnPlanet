# Security group rule allows ingress from public internet

The code examples below allows all outbound traffic from the associated EC2 instances or other resources that are associated with this security group

```terraform
 resource "aws_security_group" "insecure_example" {
    egress {
        cidr_blocks = ["0.0.0.0/0"]
    }
 }
```

# Why it's vulnerable?
Opening up ports to the public internet is generally to be avoided. You should restrict access to IP addresses or ranges explicitly requiring it where possible.

# How to fix?

Restricts egress traffic to a specific IP address

```terraform
 resource "aws_security_group" "secure_example" {
    egress {
        cidr_blocks = ["1.2.3.4/32"]
    }
 }
```


