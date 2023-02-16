# Listener for application load balancer does not use HTTPS

The code example below use HTTP

```terraform
resource "aws_alb_listener" "insecure_example" {
    protocol = "HTTP"
 }
```

# Why it's vulnerable?

If a malicious actor were to eavesdrop on your connection, they would be able to see all of your data flowing back and forth.

# How to fix?

Use HTTPS instead

```terraform
 resource "aws_alb_listener" "secure_example" {
    protocol = "HTTPS"
 }
```


