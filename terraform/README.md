# Terraform Learn

What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool created by HashiCorp
.

It allows you to create, manage, and automate infrastructure using code instead of manually clicking in cloud portals.

What Terraform Can Create

Terraform can provision:
- AWS resources
- Azure resources
- Google Cloud resources
- Kubernetes
- Docker containers
- Networks
- VPCs
- EC2 instances
- Security Groups
- Load Balancers
- Databases

Why Use Terraform
1. Automation
Deploy infrastructure quickly.
2. Consistency
Avoid manual mistakes.
3. Reusable
Use the same configuration many times.
Example:

- Splunk lab
- Dev environment
- Production environment
4. Version Control
Store infrastructure code in GitHub.
Track:

- Changes
- Rollbacks
- Team collaboration

## Terraform Workflow
1. Write Code
Create .tf files.
```
main.tf
variables.tf
outputs.tf
```
2. Initialize Terraform
```
terraform init
```

Downloads providers/plugins.

Example:

- AWS provider
- Azure provider
- GCP provider
3. Validate
Checks syntax.
```
terraform validate
```
4. Plan
```
terraform plan
```
Shows:

- What will be created
- Modified
- Destroyed
5. Apply
Actually creates resources.
```
terraform apply
```
6. Destroy
Deletes infrastructure.
```
terraform destroy
```
## Core Terraform Components
Provider - Connects Terraform to cloud/platform
Resource - Infrastructure object
Variable - Reusable input
Output - Displays values
Module - Reusable Terraform code
State File - Tracks infrastructure
## Important Terraform Files
main.tf	Main - resources
variables.tf - Variables
outputs.tf - Outputs
terraform.tfstate - Infrastructure state tracking
## Common Terraform Commands
```
terraform init
terraform plan
terraform apply
terraform destroy
terraform validate
terraform fmt
```
## Terraform Data Types

Terraform uses different data types to store and organize values inside variables, resources, locals, and outputs.

1. STRING
- A string is plain text.
```
variable "instance_name" {
  type    = string
  default = "splunk-indexer-01"
}
```
2. NUMBER
- Used for integers or decimal values.
```
variable "instance_count" {
  type    = number
  default = 3
}
```
count = 2
BOOLEAN
- Boolean values are only: true or false
```
variable "enable_monitoring" {
  type    = bool
  default = true
}
```
4. LIST
- A list stores multiple values in order.
```
variable "security_groups" {
  type    = list(string)
  default = ["sg-main", "sg-default"]
}
```
```
availability_zones = [
  "us-east-1a",
  "us-east-1b",
  "us-east-1c"
]
```
Access list items:
```
var.security_groups[0]
```
5. MAP
- A map stores key-value pairs.

```
variable "instance_tags" {
  type = map(string)

  default = {
    Environment = "Production"
    Owner       = "Michael"
    Team        = "SOC"
  }
}
```
Access values:
```
var.instance_tags["Owner"]
```
6. OBJECT
- An object groups multiple different data types together.
```
variable "server_config" {
  type = object({
    name   = string
    cpu    = number
    public = bool
  })

  default = {
    name   = "splunk-sh1"
    cpu    = 4
    public = false
  }
}
```
7. TUPLE
- A tuple is like a list, but each position can have a different type.
```
variable "server_info" {
  type = tuple([string, number, bool])

  default = ["splunk-idx1", 4, true]
}
```
8. SET
- A set is similar to a list but does NOT allow duplicates.
```
variable "subnets" {
  type = set(string)

  default = [
    "subnet-a",
    "subnet-b",
    "subnet-a"
  ]
}
```
