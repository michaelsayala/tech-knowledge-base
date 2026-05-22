# SSH Key Pairs and AWS EC2 Access

## 1. Generating SSH Key Pairs
> Terraform can create EC2 instances, but you still need an SSH key pair to securely access Linux instances.

Generate a Key Pair
```
ssh-keygen -t rsa -b 2048
```

Generate a Key Pair with a Custom Name and Location
```
ssh-keygen -t rsa -b 2048 -f ~/.ssh/splunklab_ssh
```

output:
```
~/.ssh/splunklab_ssh       # Private Key
~/.ssh/splunklab_ssh.pub   # Public Key
```

| File | Purpose |
| -------- | -------- |
| splunklab_ssh | Private key used to connect to EC2 |
| splunklab_ssh.pub | Public key uploaded to AWS |

## 2. Understanding Public vs Private Keys
Public Key
> The public key is safe to share and upload to AWS.

Private Key
> The private key must remain secure and should never be shared.

Used when connecting:
```
ssh -i splunklab_ssh ec2-user@<public-ip>
```


## 3. Import Existing Public Key to Terraform code

main.tf
```
resource "aws_key_pair" "my_key" {
  key_name   = "splunklab_ssh"
  public_key = file("${path.module}/splunklab_ssh.pub")
}
```

ec2.tf
```
resource "aws_instance" "splunk_server" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.medium"

  key_name = "splunklab_ssh"

  tags = {
    Name = "splunk-server"
  }
}
```

## 4. Create Key Pair from Variable

variables.tf
```
variable "public_key_path" {
  description = "Path to public key"
  type        = string
}
```
terraform.tfvars

```
public_key_path = "~/.ssh/splunklab_ssh.pub"
```

main.tf
```
resource "aws_key_pair" "my_key" {
  key_name   = "splunklab_ssh"
  public_key = file(var.public_key_path)
}
```

## 5. Connecting to the EC2 Instance

Update the permission of the key before connecting.
```
chmod 400 aws_key
```

```
ssh -i splunklab_ssh ec2-user@<public-ip>
```

### Best Practices

- Store only the public key (splunklab_ssh.pub) in your Terraform project.
- Keep the private key (aws_key) outside the Terraform repository.
- Never commit private keys to Git.
- Use separate key pairs for Development, Testing & Production.
- Add private keys to .gitignore
