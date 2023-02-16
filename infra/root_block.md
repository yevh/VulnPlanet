# Root block device is not encrypted

The code examples below block device is not encrypted

```terraform
resource "aws_instance" "insecure_example" {
  ami = "ami-7f89a64f"
  instance_type = "t1.micro"

  root_block_device {
      encrypted = false
  }

  ebs_block_device {
    device_name = "/dev/sdg"
    volume_size = 5
    volume_type = "gp2"
    delete_on_termination = false
    encrypted = false
  }
}
```

# Why it's vulnerable?
The block device could be compromised and read from

# How to fix?

Turn on encryption for all block devices

```terraform
resource "aws_instance" "secure_example" {
  ami = "ami-7f89a64f"
  instance_type = "t1.micro"

  root_block_device {
      encrypted = true
  }

  ebs_block_device {
    device_name = "/dev/sdg"
    volume_size = 5
    volume_type = "gp2"
    delete_on_termination = false
    encrypted = true
  }
}
```
