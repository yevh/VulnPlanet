# Security group rule does not have a description

In the code example security group rule does not have a description

```terraform
 resource "aws_security_group" "insecure_example" {
   name        = "http"

   ingress {
     from_port   = 80
     to_port     = 80
     protocol    = "tcp"
     cidr_blocks = [aws_vpc.main.cidr_block]
   }
 }
```

# Why it's vulnerable?

Security group rules should include a description for auditing purposes. Simplifies auditing, debugging, and managing security groups. Descriptions provide context for the firewall rule reasons.

# How to fix?

Add descriptions for all security groups rules

```terraform
 resource "aws_security_group" "secure_example" {
   name        = "http"
   description = "Allow inbound HTTP traffic"

   ingress {
     description = "HTTP from VPC"
     from_port   = 80
     to_port     = 80
     protocol    = "tcp"
     cidr_blocks = [aws_vpc.main.cidr_block]
   }
 }
```
