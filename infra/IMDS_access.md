# Token is not required for instance IMDS access

In the code examples instance metadata service can be interacted with freely

```terraform
 resource "aws_instance" "bad_example" {
     ami           = "ami-005e54dee72cc1d00"
     instance_type = "t2.micro"
 }
```

# Why it's vulnerable?
IMDS v2 (Instance Metadata Service) introduced session authentication tokens which improve security when talking to IMDS. By default ```aws_instance``` resource sets IMDS session auth tokens to be optional. To fully protect IMDS you need to enable session tokens by using ```metadata_options``` block and its ```http_tokens``` variable set to ```required```.

# How to fix?

Enable token requirement for IMDS

```terraform
 resource "aws_instance" "good_example" {
     ami           = "ami-003e36cii27cc2f00"
     instance_type = "t2.micro"
     metadata_options {
     http_tokens = "required"
     }  
 }
```


