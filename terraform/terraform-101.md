# Terraform 101

## Purpose

Terraform is an **Infrastructure as Code (IaC)** tool used to define, provision, configure, and manage infrastructure through declarative configuration files.

Instead of manually creating infrastructure through a cloud provider's console, Terraform allows infrastructure to be described as code.

For example:

```text
Terraform Configuration
        |
        v
   Terraform Plan
        |
        v
   Terraform Apply
        |
        v
Cloud Infrastructure
```

Terraform is commonly used to manage:

* Cloud infrastructure
* Virtual machines
* Networks
* Subnets
* Security groups
* Load balancers
* Databases
* DNS
* Storage
* IAM resources
* Kubernetes resources
* SaaS platforms
* Monitoring infrastructure
* Security infrastructure

### Infrastructure as Code

Traditional infrastructure deployment:

```text
Administrator
     |
     +--> AWS Console
     +--> Create VPC
     +--> Create Subnet
     +--> Create Security Group
     +--> Create EC2
     +--> Configure networking
     +--> Repeat manually
```

Terraform:

```text
Terraform Code
      |
      v
terraform plan
      |
      v
terraform apply
      |
      v
AWS Infrastructure
```

The infrastructure becomes:

* Repeatable
* Version controlled
* Reviewable
* Reusable
* Automated
* Consistent

---

# Core Terraform Concepts

Terraform's main concepts can be summarized as:

```text
Configuration
     |
     v
Provider
     |
     v
Resources
     |
     v
Variables
     |
     v
Modules
     |
     v
State
     |
     v
Plan
     |
     v
Apply
```

The most important concepts to understand are:

| Concept        | Purpose                                         |
| -------------- | ----------------------------------------------- |
| Terraform      | IaC tool                                        |
| HCL            | Configuration language                          |
| Provider       | Connects Terraform to an API/platform           |
| Resource       | Infrastructure object Terraform manages         |
| Data Source    | Reads existing information                      |
| Variable       | Input to Terraform configuration                |
| Local          | Reusable calculated value                       |
| Output         | Exposes information after deployment            |
| Module         | Reusable Terraform configuration                |
| State          | Records Terraform's knowledge of infrastructure |
| Backend        | Stores Terraform state                          |
| Plan           | Shows proposed changes                          |
| Apply          | Executes changes                                |
| Destroy        | Removes managed infrastructure                  |
| Workspace      | Separate state/configuration context            |
| Provider Alias | Allows multiple provider configurations         |

---

# Components

## 1. Terraform CLI

The Terraform CLI is the primary interface used to interact with Terraform.

Common commands:

```bash
terraform init
terraform validate
terraform fmt
terraform plan
terraform apply
terraform destroy
terraform show
terraform output
terraform state
```

Typical workflow:

```text
Write Code
   |
   v
terraform fmt
   |
   v
terraform init
   |
   v
terraform validate
   |
   v
terraform plan
   |
   v
terraform apply
```

---

# 2. Terraform Configuration

Terraform configuration files normally use the `.tf` extension.

Example:

```text
terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
```

Terraform automatically loads `.tf` files in the working directory.

---

# 3. HCL

Terraform primarily uses **HashiCorp Configuration Language (HCL)**.

Example:

```hcl
resource "aws_instance" "splunk" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "splunk-server"
  }
}
```

HCL is designed to describe infrastructure in a human-readable format.

---

# 4. Providers

Providers allow Terraform to communicate with external platforms and APIs.

Examples include:

```text
AWS
Azure
Google Cloud
Kubernetes
GitHub
Cloudflare
VMware
Datadog
```

For AWS:

```hcl
provider "aws" {
  region = var.aws_region
}
```

Terraform uses the AWS provider to communicate with AWS APIs.

Architecture:

```text
Terraform
    |
    v
AWS Provider
    |
    v
AWS API
    |
    +--> VPC
    +--> EC2
    +--> Security Groups
    +--> IAM
    +--> EBS
    +--> Other AWS Resources
```

---

# 5. Resources

Resources represent infrastructure that Terraform creates or manages.

Example:

```hcl
resource "aws_instance" "splunk" {
  ami           = var.ami_id
  instance_type = "t3.medium"
}
```

This creates an AWS EC2 instance.

Other examples:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "172.16.0.0/16"
}
```

```hcl
resource "aws_subnet" "splunk" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "172.16.10.0/24"
}
```

Resources normally follow:

```text
resource "TYPE" "NAME" {
  configuration
}
```

Example:

```text
TYPE = aws_instance
NAME = splunk
```

The Terraform address becomes:

```text
aws_instance.splunk
```

---

# 6. Data Sources

Data sources allow Terraform to retrieve information that already exists.

Example:

```hcl
data "aws_ami" "rhel" {
  most_recent = true

  owners = ["309956199498"]

  filter {
    name   = "name"
    values = ["RHEL-10*"]
  }
}
```

The data can then be referenced:

```hcl
ami = data.aws_ami.rhel.id
```

Difference:

| Resource                       | Data Source                     |
| ------------------------------ | ------------------------------- |
| Creates/manages infrastructure | Reads existing information      |
| `resource` block               | `data` block                    |
| Terraform manages lifecycle    | Terraform retrieves information |

---

# 7. Variables

Variables allow configuration to be reused without hardcoding values.

Example:

```hcl
variable "aws_region" {
  description = "AWS deployment region"
  type        = string
  default     = "ca-central-1"
}
```

Use:

```hcl
provider "aws" {
  region = var.aws_region
}
```

Another example:

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.medium"
}
```

This allows the same Terraform code to deploy different instance sizes.

---

# 8. Variable Types

Terraform supports several common types.

### String

```hcl
variable "region" {
  type = string
}
```

### Number

```hcl
variable "instance_count" {
  type = number
}
```

### Boolean

```hcl
variable "enable_monitoring" {
  type = bool
}
```

### List

```hcl
variable "availability_zones" {
  type = list(string)
}
```

### Map

```hcl
variable "common_tags" {
  type = map(string)
}
```

Example:

```hcl
common_tags = {
  Environment = "lab"
  Project     = "splunk"
  ManagedBy   = "terraform"
}
```

---

# 9. Locals

Locals define reusable values inside a Terraform configuration.

Example:

```hcl
locals {
  project_name = "splunklab"

  common_tags = {
    Environment = "lab"
    Project     = "splunk"
    ManagedBy   = "terraform"
  }
}
```

Use:

```hcl
tags = local.common_tags
```

Locals are useful when the same value is referenced throughout multiple resources.

---

# 10. Outputs

Outputs expose information after Terraform creates infrastructure.

Example:

```hcl
output "splunk_instance_ip" {
  description = "Public IP address of Splunk server"
  value       = aws_instance.splunk.public_ip
}
```

After:

```bash
terraform apply
```

Terraform may display:

```text
splunk_instance_ip = "203.0.113.10"
```

Outputs are useful for:

* IP addresses
* DNS names
* Instance IDs
* Resource IDs
* Load balancer addresses
* Integration with other automation

---

# 11. Modules

Modules allow Terraform configurations to be packaged into reusable components.

Example:

```text
modules/
├── vpc/
├── security_group/
├── ec2/
└── splunk/
```

A module can be called:

```hcl
module "splunk" {
  source = "./modules/splunk"

  instance_type = "t3.medium"
  ami_id        = var.ami_id
}
```

Modules help prevent duplicated Terraform code.

---

# 12. State

Terraform state is one of the most important Terraform concepts.

Terraform uses state to track infrastructure it manages.

Typical local state file:

```text
terraform.tfstate
```

Conceptually:

```text
Terraform Configuration
        |
        v
Terraform State
        |
        v
Real Infrastructure
```

Terraform compares:

```text
Desired State
      |
      v
terraform configuration

Current Known State
      |
      v
terraform.tfstate

Real Infrastructure
      |
      v
AWS
```

Terraform uses this information to determine what changes are required.

---

# 13. State File

A state file may contain information such as:

```text
Resource
Resource ID
Attributes
Dependencies
Provider information
Metadata
```

Example:

```text
aws_instance.splunk
    |
    +-- instance_id
    +-- private_ip
    +-- public_ip
    +-- availability_zone
```

State can contain sensitive information.

Therefore:

```text
terraform.tfstate
```

should generally **not** be committed to Git.

Use `.gitignore`:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
crash.log
```

---

# 14. Backend

A backend determines where Terraform stores state.

Local backend:

```text
Terraform
    |
    v
terraform.tfstate
    |
    v
Local filesystem
```

Remote backend:

```text
Terraform
    |
    v
Remote Backend
    |
    v
Shared State
```

Remote state is commonly used for team environments.

AWS environments may use:

```text
S3
```

for state storage, often combined with locking depending on the Terraform/AWS setup and backend configuration.

---

# 15. Dependency Graph

Terraform builds a dependency graph to determine resource relationships.

Example:

```text
VPC
 |
 +--> Subnet
       |
       +--> Security Group
       |
       +--> EC2
```

Terraform understands references such as:

```hcl
vpc_id = aws_vpc.main.id
```

This creates an implicit dependency.

Terraform therefore knows the VPC must exist before the dependent resource can be created.

---

# Architecture

## Basic Terraform Architecture

```text
+----------------------+
| Terraform CLI        |
|                      |
| .tf configuration    |
+----------+-----------+
           |
           v
+----------------------+
| Terraform Engine     |
|                      |
| Plan / Graph / State |
+----------+-----------+
           |
           v
+----------------------+
| Provider             |
| AWS / Azure / GCP    |
+----------+-----------+
           |
           v
+----------------------+
| Cloud/API Platform   |
+----------------------+
```

---

# AWS Terraform Architecture

A typical AWS deployment:

```text
                    Terraform
                        |
                        v
                 AWS Provider
                        |
                        v
                  AWS API
                        |
        +---------------+---------------+
        |               |               |
        v               v               v
       VPC             IAM             EC2
        |
        +-----------------------+
        |                       |
        v                       v
      Subnet              Security Group
        |
        v
   Splunk Servers
```

---

# Splunk + Terraform Architecture

Terraform can provision the infrastructure while Ansible configures the software.

```text
                    Terraform
                       |
                       v
                AWS Infrastructure
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
      VPC            EC2            Security
                     Instances       Groups
                       |
                       v
                    Ansible
                       |
          +------------+------------+
          |            |            |
          v            v            v
      Splunk IDX    Splunk SH    Forwarders
```

This creates a useful separation:

```text
Terraform
    |
    +--> Infrastructure
          VPC
          Subnets
          EC2
          Security Groups
          IAM

Ansible
    |
    +--> Configuration
          Splunk installation
          Cluster configuration
          SSL
          Firewall
          Forwarders
          Search Head Cluster
          Indexer Cluster
```

---

# Example Splunk AWS Architecture

A Terraform project can provision infrastructure for:

```text
AWS
|
+-- VPC
|    |
|    +-- Subnet
|
+-- Security Group
|
+-- EC2
     |
     +-- cm1
     +-- idx01
     +-- idx02
     +-- idx03
     +-- sh01
     +-- sh02
     +-- sh03
     +-- dep01
     +-- ds01
     +-- lm01
     +-- hf01
     +-- hf02
     +-- uf01
     +-- uf02
```

Terraform creates the infrastructure.

Ansible configures the Splunk services.

---

# Usage

## Initialize a Project

Create a directory:

```bash
mkdir terraform-lab
cd terraform-lab
```

Create:

```text
main.tf
```

Run:

```bash
terraform init
```

Terraform downloads the required providers and initializes the working directory.

---

# Format Configuration

Run:

```bash
terraform fmt
```

This formats Terraform configuration according to Terraform's standard formatting rules.

Check formatting:

```bash
terraform fmt -check
```

---

# Validate Configuration

Run:

```bash
terraform validate
```

This checks whether the configuration is syntactically and structurally valid.

Example:

```text
Success! The configuration is valid.
```

---

# Terraform Plan

Run:

```bash
terraform plan
```

Terraform evaluates the configuration and shows proposed changes.

Example:

```text
Plan: 3 to add, 0 to change, 0 to destroy.
```

Terraform uses:

```text
Configuration
      +
State
      +
Provider information
      |
      v
Terraform Plan
```

---

# Terraform Apply

Run:

```bash
terraform apply
```

Terraform displays the proposed changes and normally asks for confirmation.

```text
Do you want to perform these actions?
  Only 'yes' will be accepted to approve.
```

Apply:

```text
yes
```

Terraform then creates or modifies the infrastructure.

---

# Automatic Approval

For automation:

```bash
terraform apply -auto-approve
```

Use this carefully in production because it skips the interactive approval step.

---

# Destroy

To remove infrastructure managed by Terraform:

```bash
terraform destroy
```

Terraform shows the resources that will be removed.

Example:

```text
Plan: 0 to add, 0 to change, 3 to destroy.
```

---

# Targeting Resources

Terraform can target a specific resource:

```bash
terraform plan -target=aws_instance.splunk
```

However, targeted operations should generally be used carefully and not as the normal workflow because Terraform is designed to manage the complete dependency graph.

---

# Show State

View Terraform state:

```bash
terraform show
```

List resources:

```bash
terraform state list
```

Example:

```text
aws_vpc.main
aws_subnet.splunk
aws_security_group.splunk
aws_instance.idx01
aws_instance.idx02
aws_instance.idx03
```

---

# Terraform Console

Terraform provides an interactive console:

```bash
terraform console
```

This can be useful for testing expressions.

Example:

```text
> var.aws_region
"ca-central-1"
```

---

# Configuration

## Recommended Project Structure

A small Terraform project:

```text
terraform/
├── versions.tf
├── providers.tf
├── variables.tf
├── main.tf
├── outputs.tf
├── terraform.tfvars
└── .gitignore
```

A larger project:

```text
terraform/
├── environments/
│   ├── dev/
│   ├── test/
│   └── prod/
│
├── modules/
│   ├── vpc/
│   ├── security_group/
│   ├── ec2/
│   └── splunk/
│
├── versions.tf
├── providers.tf
├── variables.tf
├── main.tf
├── outputs.tf
└── README.md
```

---

# versions.tf

Define Terraform and provider requirements.

Example:

```hcl
terraform {
  required_version = ">= 1.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

Provider versions should be deliberately controlled in production rather than allowing unexpected upgrades.

---

# providers.tf

Example:

```hcl
provider "aws" {
  region = var.aws_region
}
```

---

# variables.tf

Example:

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "ca-central-1"
}

variable "vpc_cidr" {
  description = "VPC CIDR"
  type        = string
  default     = "172.16.0.0/16"
}

variable "subnet_cidr" {
  description = "Splunk subnet CIDR"
  type        = string
  default     = "172.16.10.0/24"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.medium"
}
```

---

# main.tf

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "splunklab-vpc"
  }
}

resource "aws_subnet" "splunk" {
  vpc_id     = aws_vpc.main.id
  cidr_block = var.subnet_cidr

  tags = {
    Name = "splunklab-subnet"
  }
}
```

---

# outputs.tf

Example:

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.main.id
}

output "subnet_id" {
  description = "Splunk subnet ID"
  value       = aws_subnet.splunk.id
}
```

---

# terraform.tfvars

Example:

```hcl
aws_region   = "ca-central-1"
vpc_cidr     = "172.16.0.0/16"
subnet_cidr  = "172.16.10.0/24"
instance_type = "t3.medium"
```

Do not commit sensitive values to Git.

For example:

```gitignore
terraform.tfvars
*.tfstate
*.tfstate.*
```

---

# Resource Meta-Arguments

Terraform resources support important meta-arguments.

## count

Create multiple resources:

```hcl
resource "aws_instance" "splunk" {
  count = 3

  ami           = var.ami_id
  instance_type = var.instance_type
}
```

Resources become:

```text
aws_instance.splunk[0]
aws_instance.splunk[1]
aws_instance.splunk[2]
```

---

# for_each

`for_each` is useful when each resource needs a unique identity.

Example:

```hcl
variable "splunk_nodes" {
  default = {
    idx01 = "t3.medium"
    idx02 = "t3.medium"
    idx03 = "t3.medium"
  }
}
```

Resource:

```hcl
resource "aws_instance" "splunk" {
  for_each = var.splunk_nodes

  ami           = var.ami_id
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

This produces:

```text
idx01
idx02
idx03
```

---

# depends_on

Terraform normally determines dependencies automatically.

Explicit dependency:

```hcl
resource "aws_instance" "splunk" {
  depends_on = [
    aws_security_group.splunk
  ]

  ami           = var.ami_id
  instance_type = var.instance_type
}
```

Use `depends_on` when Terraform cannot infer the dependency from resource references.

---

# lifecycle

Terraform lifecycle settings control resource behavior.

Example:

```hcl
lifecycle {
  create_before_destroy = true
}
```

Another option:

```hcl
lifecycle {
  prevent_destroy = true
}
```

This can help protect important resources from accidental deletion.

---

# Tags

Tags are extremely useful in AWS environments.

Example:

```hcl
locals {
  common_tags = {
    Project     = "Splunk Lab"
    Environment = "Lab"
    ManagedBy   = "Terraform"
    Owner       = "Security Engineering"
  }
}
```

Use:

```hcl
tags = local.common_tags
```

Resource-specific tags can be added:

```hcl
tags = merge(
  local.common_tags,
  {
    Name = "idx01"
    Role = "splunk-indexer"
  }
)
```

---

# Dependencies

Terraform itself is the primary automation engine, but infrastructure deployment usually depends on several external components.

## Terraform Dependencies

```text
Terraform CLI
     |
     +-- Provider
     |
     +-- Cloud API
     |
     +-- Credentials
     |
     +-- Network
     |
     +-- State Backend
```

---

# Cloud Provider

Terraform requires a provider for the platform being managed.

For AWS:

```text
Terraform
    |
    v
AWS Provider
    |
    v
AWS API
```

---

# Credentials

Terraform needs authentication to the target platform.

AWS credentials can be provided through mechanisms such as:

```text
AWS CLI configuration
Environment variables
IAM roles
Instance profiles
OIDC-based authentication
```

Avoid hardcoding credentials:

```hcl
access_key = "..."
secret_key = "..."
```

Prefer secure credential mechanisms.

---

# Network Connectivity

Terraform must be able to communicate with the provider API.

For AWS:

```text
Terraform
    |
    v
Internet / AWS connectivity
    |
    v
AWS API
```

---

# Git

Terraform configuration should normally be stored in version control.

Example:

```text
Git
 |
 +-- main.tf
 +-- variables.tf
 +-- outputs.tf
 +-- providers.tf
 +-- modules/
```

Git provides:

* Version history
* Change tracking
* Code review
* Collaboration
* Rollback reference
* Auditability

---

# Terraform + Ansible

Terraform and Ansible solve different parts of infrastructure automation.

## Terraform

Terraform is primarily used for:

```text
Infrastructure Provisioning
```

Examples:

```text
VPC
Subnet
EC2
Security Groups
IAM
Load Balancers
Storage
```

## Ansible

Ansible is primarily used for:

```text
Configuration Management
```

Examples:

```text
OS configuration
Package installation
Splunk installation
Configuration files
Firewall
SSL
Cluster configuration
Application configuration
```

Combined:

```text
                    Git
                     |
          +----------+----------+
          |                     |
          v                     v
      Terraform               Ansible
          |                     |
          v                     v
 Infrastructure             Configuration
          |                     |
          v                     v
        AWS                  Splunk
```

This is a common and useful separation of responsibilities.

---

# Terraform + Splunk Example

For a Splunk distributed deployment:

```text
Terraform
|
+-- VPC
|
+-- Subnet
|
+-- Security Groups
|
+-- EC2 Instances
     |
     +-- cm1
     +-- idx01
     +-- idx02
     +-- idx03
     +-- sh01
     +-- sh02
     +-- sh03
     +-- dep01
     +-- ds01
     +-- lm01
     +-- hf01
     +-- hf02
     +-- uf01
     +-- uf02
```

Then:

```text
Ansible
|
+-- OS configuration
+-- Splunk installation
+-- SSL
+-- Firewall
+-- Indexer Cluster
+-- Search Head Cluster
+-- Deployment Server
+-- Universal Forwarders
```

This approach keeps infrastructure provisioning separate from application configuration.

---

# Security

Terraform should be treated as production infrastructure code.

## Protect Credentials

Do not hardcode:

```text
AWS Access Keys
Passwords
API Tokens
Private Keys
Certificates
Secrets
```

Bad:

```hcl
variable "password" {
  default = "SuperSecretPassword"
}
```

Better:

```hcl
variable "password" {
  type      = string
  sensitive = true
}
```

Use an external secret-management system where appropriate.

---

# Protect Terraform State

Terraform state may contain sensitive information.

Consider:

```text
Encryption
Access control
Remote state
State locking
Restricted permissions
Audit logging
```

Do not casually expose:

```text
terraform.tfstate
```

---

# IAM Permissions

Terraform should use the minimum permissions required for its intended resources.

For example:

```text
Terraform IAM Role
       |
       +-- VPC permissions
       +-- EC2 permissions
       +-- Security Group permissions
       +-- IAM permissions
```

Avoid using unrestricted administrator credentials for normal automation when a narrower role can accomplish the task.

---

# Security Groups

Terraform can manage AWS security groups.

Example:

```hcl
resource "aws_security_group" "splunk" {
  name   = "splunk-security-group"
  vpc_id = aws_vpc.main.id

  ingress {
    description = "Splunk Web"
    from_port   = 8000
    to_port     = 8000
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/8"]
  }
}
```

Only expose required ports and trusted source networks.

---

# Sensitive Variables

Mark sensitive outputs:

```hcl
output "secret_value" {
  value     = var.secret_value
  sensitive = true
}
```

This helps prevent Terraform from displaying the value normally in CLI output.

---

# License / Cost

Terraform itself has different distribution and product considerations depending on the Terraform edition/version and HashiCorp offerings in use.

For infrastructure automation, cost should be considered at two levels:

```text
Terraform
+
Infrastructure
```

## Terraform Cost

Terraform is an infrastructure automation tool; using Terraform does not make the underlying infrastructure free.

For example:

```text
Terraform
    |
    v
AWS
    |
    +-- EC2
    +-- EBS
    +-- VPC-related services
    +-- Data transfer
    +-- Load Balancers
    +-- NAT Gateway
    +-- Other services
```

The AWS resources still incur their normal charges.

---

# Infrastructure Cost

For an AWS Splunk lab, major cost factors can include:

| Resource      | Potential Cost                     |
| ------------- | ---------------------------------- |
| EC2           | Compute                            |
| EBS           | Storage                            |
| Elastic IP    | Depending on usage/current pricing |
| NAT Gateway   | Hourly + data processing           |
| Data Transfer | Network usage                      |
| S3            | State/storage                      |
| CloudWatch    | Monitoring/logging                 |
| Splunk        | Separate licensing considerations  |

A large distributed Splunk lab can become expensive because it may require many EC2 instances.

For example:

```text
cm1
idx01
idx02
idx03
sh01
sh02
sh03
dep01
ds01
lm01
hf01
hf02
uf01
uf02
```

means many infrastructure resources may be running simultaneously.

---

# Performance

Terraform performance is generally affected by:

* Number of resources
* Provider API performance
* API rate limits
* Dependency graph complexity
* Module complexity
* State size
* Backend performance
* Number of parallel operations

Terraform can perform independent operations in parallel when dependencies allow it.

Conceptually:

```text
VPC
 |
 +---- Subnet
 |
 +---- IAM
 |
 +---- Security Group
 |
 +---- Other independent resources
```

Independent resources may be processed concurrently.

---

# Parallelism

Terraform supports parallel resource operations.

Example:

```bash
terraform apply -parallelism=10
```

The default parallelism should generally be sufficient.

Increasing parallelism does not automatically make deployments better because provider API limits and infrastructure dependencies still apply.

---

# Reliability

Terraform improves infrastructure consistency by allowing infrastructure to be recreated from code.

Example:

```text
Terraform Code
      |
      v
AWS Environment
```

If the environment needs to be recreated:

```text
Terraform Code
      |
      v
New AWS Environment
```

This is one of the major benefits of Infrastructure as Code.

---

# Idempotency

Terraform is designed around desired state.

Example:

```text
Desired:
3 Splunk Indexers
```

Current:

```text
3 Splunk Indexers
```

Terraform:

```text
No changes
```

If only two exist:

```text
Desired: 3
Current: 2
```

Terraform can determine that another resource needs to be created.

---

# Drift

Infrastructure can change outside Terraform.

Example:

```text
Terraform
   |
   v
AWS EC2
```

An administrator manually changes:

```text
Instance Type
```

Terraform configuration still says:

```text
t3.medium
```

but AWS contains:

```text
t3.large
```

This creates configuration drift.

Terraform can detect differences during:

```bash
terraform plan
```

---

# Monitoring

Terraform itself should be monitored as part of the infrastructure deployment process.

Useful areas include:

```text
Terraform Plan
Terraform Apply
Terraform State
Provider Errors
Cloud Provider Events
CI/CD Pipeline Results
Infrastructure Health
```

For enterprise environments, infrastructure changes should ideally be traceable to:

```text
Person
    |
    v
Git Change
    |
    v
Terraform Plan
    |
    v
Approval
    |
    v
Terraform Apply
    |
    v
Infrastructure
```

---

# Troubleshooting

## terraform init fails

Run:

```bash
terraform init
```

Check:

```text
Internet connectivity
Provider configuration
Provider version
Backend configuration
Credentials
```

---

## terraform validate fails

Run:

```bash
terraform validate
```

Check:

```text
Syntax
Variable definitions
Resource references
Provider configuration
Module configuration
```

---

## terraform plan shows unexpected changes

Check:

```bash
terraform plan
```

Then:

```bash
terraform show
```

and:

```bash
terraform state list
```

Potential causes:

```text
Configuration drift
Changed variables
Provider changes
Resource replacement
Incorrect state
Changed defaults
```

---

# Resource Will Be Replaced

Terraform may show:

```text
-/+ resource
```

This usually means Terraform intends to destroy and recreate the resource.

Pay close attention to:

```text
forces replacement
```

before running:

```bash
terraform apply
```

---

# State Lock Problems

If using remote state and state locking is enabled, Terraform may report that the state is locked.

Do not immediately force-unlock without understanding why the lock exists.

First determine:

```text
Is another Terraform operation running?
Did a previous operation fail?
Is the lock stale?
```

---

# Credentials Not Found

AWS authentication errors may indicate that Terraform cannot find valid credentials.

Check:

```bash
aws sts get-caller-identity
```

If the AWS CLI can authenticate but Terraform cannot, review the Terraform provider configuration and credential environment.

---

# Provider Errors

Example:

```text
Error: creating EC2 instance
```

Investigate:

```text
AWS permissions
AWS region
AMI availability
Instance limits
VPC configuration
Subnet configuration
Security groups
Service quotas
```

---

# AWS Service Quotas

Cloud providers impose resource limits.

For example:

```text
EC2 vCPU limits
Elastic IP limits
VPC limits
Subnet limits
Security group limits
API rate limits
```

Terraform cannot bypass these limits.

For a large Splunk lab, check AWS service quotas before provisioning many instances.

---

# Common Terraform Commands

| Command                | Purpose                        |
| ---------------------- | ------------------------------ |
| `terraform init`       | Initialize project             |
| `terraform fmt`        | Format code                    |
| `terraform validate`   | Validate configuration         |
| `terraform plan`       | Preview changes                |
| `terraform apply`      | Apply changes                  |
| `terraform destroy`    | Destroy resources              |
| `terraform show`       | Display state/plan             |
| `terraform output`     | Display outputs                |
| `terraform state list` | List managed resources         |
| `terraform state show` | Show resource state            |
| `terraform providers`  | Show providers                 |
| `terraform graph`      | Generate dependency graph      |
| `terraform console`    | Interactive expression console |
| `terraform version`    | Show Terraform version         |

---

# Recommended Terraform Workflow

A practical workflow is:

```text
1. Write Terraform code
          |
          v
2. terraform fmt
          |
          v
3. terraform init
          |
          v
4. terraform validate
          |
          v
5. terraform plan
          |
          v
6. Review changes
          |
          v
7. terraform apply
          |
          v
8. Validate infrastructure
          |
          v
9. Configure infrastructure
```

For your Splunk lab:

```text
Terraform
   |
   +--> AWS VPC
   +--> Subnet
   +--> Security Groups
   +--> EC2
   |
   v
Ansible
   |
   +--> RHEL Configuration
   +--> Splunk Installation
   +--> SSL
   +--> Indexer Cluster
   +--> Search Head Cluster
   +--> Forwarders
```

---

# Terraform vs Ansible

| Area                      | Terraform                   | Ansible                        |
| ------------------------- | --------------------------- | ------------------------------ |
| Primary purpose           | Infrastructure provisioning | Configuration management       |
| IaC                       | Yes                         | Yes                            |
| Cloud resources           | Strong                      | Supported                      |
| OS configuration          | Limited                     | Strong                         |
| Application configuration | Limited                     | Strong                         |
| State                     | Uses state                  | Generally agentless/task-based |
| Agent required            | No                          | No                             |
| AWS EC2                   | Strong                      | Supported                      |
| Splunk installation       | Possible                    | Strong                         |
| Splunk configuration      | Limited                     | Strong                         |
| Network automation        | Supported                   | Strong                         |
| Infrastructure lifecycle  | Strong                      | Less focused                   |
| Configuration drift       | Detects via plan/state      | Depends on playbook design     |

A useful mental model:

```text
Terraform = Build the infrastructure

Ansible = Configure the infrastructure
```

---

# Terraform vs Manual Deployment

## Manual

```text
AWS Console
   |
   +--> VPC
   +--> Subnet
   +--> Security Group
   +--> EC2
   +--> IAM
   |
   v
Manual configuration
```

Problems can include:

```text
Human error
Configuration inconsistency
Poor repeatability
Difficult auditing
Slow deployment
```

## Terraform

```text
Terraform Code
      |
      v
terraform plan
      |
      v
terraform apply
      |
      v
Consistent Infrastructure
```

---

# Enterprise Terraform Architecture

A mature environment may look like:

```text
                         Git
                          |
                          v
                  Terraform Repository
                          |
                          v
                    CI/CD Pipeline
                          |
                    +-----+-----+
                    |           |
                    v           v
                  Plan       Approval
                    |           |
                    +-----+-----+
                          |
                          v
                        Apply
                          |
             +------------+------------+
             |            |            |
             v            v            v
            AWS          Azure        Other
             |
             v
        Remote State
             |
             v
      State Management
```

Additional enterprise controls may include:

```text
Code Review
Policy Checks
Security Scanning
Secret Management
Remote State
State Locking
Audit Logging
Approval Workflows
Drift Detection
```

---

# Best Practices

## 1. Use Version Control

Store Terraform code in Git.

```text
Git
 |
 +-- Terraform configuration
 +-- Modules
 +-- Documentation
```

---

## 2. Use Modules

Avoid duplicating large amounts of configuration.

Instead of:

```text
1000 lines repeated
```

use:

```text
Reusable Module
      |
      +--> Environment 1
      +--> Environment 2
      +--> Environment 3
```

---

## 3. Pin Versions

Control Terraform and provider versions.

```hcl
terraform {
  required_version = ">= 1.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

Choose version constraints appropriate for your environment and test upgrades before applying them broadly.

---

## 4. Use Variables

Avoid hardcoding values that should change between environments.

Instead of:

```hcl
region = "ca-central-1"
```

use:

```hcl
region = var.aws_region
```

---

## 5. Use Outputs

Expose important infrastructure information.

```hcl
output "instance_ip" {
  value = aws_instance.splunk.public_ip
}
```

---

## 6. Protect State

Treat state as sensitive infrastructure data.

```text
Do not commit:
terraform.tfstate
terraform.tfstate.*
```

---

## 7. Review Plans

Always understand:

```text
What will be created?
What will change?
What will be destroyed?
```

before applying infrastructure changes.

---

## 8. Use Least Privilege

Terraform credentials should have only the permissions required for the infrastructure they manage.

---

## 9. Use Tags

Tag cloud resources consistently.

Example:

```text
Environment
Project
Owner
ManagedBy
Role
Application
CostCenter
```

---

## 10. Separate Environments

Avoid accidentally deploying development configuration into production.

Possible structure:

```text
environments/
├── dev/
├── test/
└── prod/
```

---

# Terraform Mental Model

The easiest way to understand Terraform is:

```text
Desired State
     |
     v
Terraform Configuration
     |
     v
Terraform Plan
     |
     v
Terraform Apply
     |
     v
Real Infrastructure
     |
     v
Terraform State
```

Terraform continuously uses these concepts to determine:

```text
What exists?
What should exist?
What changed?
What needs to change?
```

---

# Quick Reference

## Create Infrastructure

```bash
terraform init
terraform plan
terraform apply
```

## Validate Code

```bash
terraform fmt
terraform validate
```

## Inspect

```bash
terraform show
terraform output
terraform state list
```

## Destroy

```bash
terraform destroy
```

## Debug

```bash
terraform plan
terraform show
terraform state list
terraform state show RESOURCE
```

---

# Terraform + Splunk Lab Mental Model

For your Splunk AWS project, the architecture can be summarized as:

```text
                         Git
                          |
                          v
                    Terraform Code
                          |
                          v
                    terraform plan
                          |
                          v
                   terraform apply
                          |
                          v
                         AWS
                          |
        +-----------------+-----------------+
        |                 |                 |
        v                 v                 v
       VPC              Network          Security
        |                                  Groups
        |
        v
       EC2
        |
        +----------------+----------------+
        |                |                |
        v                v                v
   Splunk Servers     Forwarders       Supporting
                                       Services
        |
        v
      Ansible
        |
        +--> Splunk Installation
        +--> Indexer Cluster
        +--> Search Head Cluster
        +--> Deployment Server
        +--> SSL
        +--> Firewall
        +--> Forwarder Configuration
```

This gives you a clear separation:

```text
Terraform
    =
Infrastructure

Ansible
    =
Configuration

Splunk
    =
Observability / Security Platform
```

---

# Summary

Terraform is an Infrastructure as Code platform that allows infrastructure to be defined, provisioned, and managed through code.

The core concepts to understand are:

```text
Providers
Resources
Data Sources
Variables
Locals
Outputs
Modules
State
Backends
Dependencies
Plan
Apply
Destroy
```

For an AWS-based Splunk environment, Terraform can manage:

```text
VPC
Subnets
Security Groups
EC2
IAM
Storage
Networking
```

while Ansible can handle:

```text
Operating System Configuration
Splunk Installation
Splunk Configuration
SSL
Firewall
Indexer Cluster
Search Head Cluster
Forwarders
```

The resulting automation architecture is:

```text
                    Git
                     |
          +----------+----------+
          |                     |
          v                     v
      Terraform               Ansible
          |                     |
          v                     v
   AWS Infrastructure      Splunk Configuration
          |                     |
          +----------+----------+
                     |
                     v
             Splunk Environment
```

This Terraform + Ansible separation provides a practical foundation for building repeatable Splunk infrastructure in AWS.
