# Access keys for the root is present

In the code example ```root``` user has complete access to all services and resources in an AWS account. AWS Access Keys provide programmatic access to a given account.

```terraform
resource "aws_iam_access_key" "insecure_example" {
    user = "root"
}
```

# Why it's vulnerable?

CIS recommends that all access keys be associated with the ```root``` user be removed. Removing access keys associated with the root user limits vectors that the account can be compromised by. Removing the root user access keys also encourages the creation and use of role-based accounts that are least privileged. Compromise of the root account compromises the entire AWS account and all resources within it.

# How to fix?

Use lower privileged accounts instead, so only required privileges are available

```terraform
resource "aws_iam_access_key" "secure_example" {
    user = "lowprivuser"
}
```
