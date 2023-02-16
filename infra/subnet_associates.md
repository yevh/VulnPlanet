# Subnet associates public IP address

In the code example instance is publicly accessible

```terraform
 resource "aws_subnet" "insecure_example" {
    vpc_id                  = "vpc-987654"
    map_public_ip_on_launch = true
 }
```

# Why it's vulnerable?
You should limit the provision of public IP addresses for resources. Resources should not be exposed on the public internet, but should have access limited to consumers required for the function of your application.

# How to fix?

The instance to not be publicly accessible

```terraform
 resource "aws_subnet" "secure_example" {
    vpc_id                  = "vpc-987654"
    map_public_ip_on_launch = false
 }
```
