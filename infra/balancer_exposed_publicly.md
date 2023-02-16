# Load balancer is exposed publicly

In the code example below load balancer is exposed on the internet

```terraform
 resource "aws_alb" "insecure_example" {
    internal = false
 }

```

# Why it's vulnerable?
There are many scenarios in which you would want to expose a load balancer to the wider internet, but this check exists as a warning to prevent accidental exposure of internal assets. You should ensure that this resource should be exposed publicly.

# How to fix?

Load balancer is not exposed

```terraform
 resource "aws_alb" "secure_example" {
    internal = true
 }
```
