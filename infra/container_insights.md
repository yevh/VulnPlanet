# Cluster does not have container insights enabled

In the code example cluster does not have container insights enabled

```terraform
 resource "aws_ecs_cluster" "insecure_example" {
    name = "services-cluster"
 }
```

# Why it's vulnerable?

Cloudwatch Container Insights provide more metrics and logs for container based applications and micro services. Not all metrics and logs may be gathered for containers when Container Insights isn't enabled.

# How to fix?

Enable Container Insights

```terraform
 resource "aws_ecs_cluster" "secure_example" {
    name = "services-cluster"

    setting {
      name  = "containerInsights"
      value = "enabled"
    }
 }
```
