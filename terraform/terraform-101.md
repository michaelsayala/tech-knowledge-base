# Terraform

> Foundational technical documentation covering Terraform purpose, components, architecture, usage, configuration, dependencies, and licensing.

---

# 1. Purpose

## 1.1 What is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool used to **provision, configure, manage, and automate infrastructure** using declarative configuration files.

Terraform can be used to manage:

* Cloud infrastructure
* Virtual machines
* Networks
* Subnets
* Security groups
* Load balancers
* Storage
* Databases
* DNS
* Kubernetes resources
* SaaS platforms
* Monitoring resources
* Identity resources
* Infrastructure services

Instead of manually creating infrastructure:

```text
AWS Console
    │
    ├── Create VPC
    ├── Create Subnet
    ├── Create Security Group
    ├── Create EC2
    ├── Configure Storage
    └── Configure Networking
```

Terraform allows infrastructure to be defined as code:

```text
              Terraform
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
         VPC    Subnet    EC2
          │       │        │
          └───────┼────────┘
                  ▼
           Infrastructure
```

---

## 1.2 Infrastructure Automation Problem

Without Infrastructure as Code, infrastructure can become difficult to reproduce and maintain.

Example:

```text
AWS Environment

VPC
 ├── Created manually
 ├── Unknown configuration
 ├── Unknown dependencies
 └── Difficult to reproduce

EC2
 ├── Manually configured
 ├── Different instance settings
 └── Configuration drift
```

Terraform allows the infrastructure configuration to be defined in code:

```text
Infrastructure Code
        │
        ▼
    Terraform
        │
        ▼
   Cloud Provider
        │
        ▼
 Consistent Infrastructure
```

---

## 1.3 Infrastructure as Code

Infrastructure as Code means defining infrastructure using machine-readable configuration files.

Example:

```hcl
resource "aws_instance" "splunk" {
  ami           = var.ami_id
  instance_type = "t3.medium"

  tags = {
    Name = "splunk-server"
  }
}
```

Instead of manually creating the server, Terraform interprets the configuration and creates the required infrastructure.

The basic concept is:

```text
Desired Infrastructure
        ↓
    Terraform
        ↓
Cloud Provider
        ↓
Actual Infrastructure
```

---

## 1.4 Provisioning

Terraform is commonly used to provision infrastructure.

Example:

```text
Terraform
    │
    ├── Create VPC
    ├── Create Subnet
    ├── Create Security Group
    ├── Create EC2 Instances
    ├── Create Storage
    └── Configure Networking
    │
    ▼
Infrastructure Ready
```

For an AWS Splunk environment:

```text
Terraform
    │
    ├── VPC
    ├── Subnet
    ├── Security Groups
    ├── EC2 Instances
    ├── IAM
    └── Networking
         │
         ▼
    Splunk Infrastructure
```

---

## 1.5 Declarative Configuration

Terraform uses a **declarative approach**.

You define **what the infrastructure should look like**, rather than describing every individual command required to create it.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = "t3.medium"
}
```

You do not normally specify:

```text
1. Open AWS Console
2. Click EC2
3. Click Launch Instance
4. Select AMI
5. Select Instance Type
6. Configure Network
7. Configure Storage
8. Launch
```

Instead:

```text
Desired State
      │
      ▼
   Terraform
      │
      ▼
Terraform Provider
      │
      ▼
    AWS API
      │
      ▼
Infrastructure
```

---

## 1.6 Infrastructure Lifecycle

Terraform can manage the infrastructure lifecycle.

```text
                Terraform
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
      Create      Update       Destroy
        │           │           │
        ▼           ▼           ▼
 Infrastructure  Changes    Infrastructure
```

Typical lifecycle:

```text
DEFINE
   ↓
PLAN
   ↓
APPLY
   ↓
MANAGE
   ↓
UPDATE
   ↓
DESTROY
```

---

## 1.7 Common Use Cases

| Use Case                       | Description                                           |
| ------------------------------ | ----------------------------------------------------- |
| Infrastructure Provisioning    | Create cloud and infrastructure resources             |
| Cloud Automation               | Automate AWS, Azure, GCP, and other platforms         |
| Network Provisioning           | Create VPCs, subnets, routes, and security controls   |
| Server Provisioning            | Create virtual machines and instances                 |
| Storage Provisioning           | Create disks, buckets, and storage resources          |
| Database Provisioning          | Create managed database infrastructure                |
| IAM Automation                 | Manage identities and permissions                     |
| Kubernetes                     | Provision and manage Kubernetes infrastructure        |
| Infrastructure Standardization | Maintain consistent environments                      |
| Disaster Recovery              | Recreate infrastructure from code                     |
| Environment Management         | Create development, test, and production environments |
| Infrastructure Lifecycle       | Create, update, and destroy infrastructure            |

---

# 2. Components

The major Terraform concepts are:

```text
Terraform
│
├── Terraform CLI
├── Configuration
├── HCL
├── Providers
├── Resources
├── Data Sources
├── Variables
├── Locals
├── Outputs
├── Modules
├── State
├── Backend
├── Dependencies
├── Plan
└── Apply
```

---

# 2.1 Terraform CLI

The Terraform CLI is the command-line interface used to interact with Terraform.

Common commands include:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
```

The CLI is used to:

```text
Initialize
   ↓
Validate
   ↓
Plan
   ↓
Apply
   ↓
Manage
   ↓
Destroy
```

---

# 2.2 Terraform Configuration

Terraform configuration files define the desired infrastructure.

Terraform configuration files normally use:

```text
.tf
```

Example:

```text
main.tf
variables.tf
outputs.tf
providers.tf
```

Example:

```hcl
resource "aws_instance" "splunk" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

---

# 2.3 HCL

Terraform commonly uses **HashiCorp Configuration Language (HCL)**.

Example:

```hcl
variable "instance_type" {
  type    = string
  default = "t3.medium"
}
```

HCL is designed to be readable by humans while remaining machine-processable.

---

# 2.4 Providers

Providers allow Terraform to communicate with external APIs.

Examples include providers for:

```text
AWS
Azure
Google Cloud
Kubernetes
GitHub
Cloudflare
Datadog
```

Example:

```hcl
provider "aws" {
  region = "ca-central-1"
}
```

The provider acts as the connection between Terraform and the external platform.

```text
Terraform
    │
    ▼
 Provider
    │
    ▼
 External API
    │
    ▼
Infrastructure
```

---

# 2.5 Resources

Resources represent infrastructure objects that Terraform manages.

Example:

```hcl
resource "aws_instance" "splunk" {
  ami           = var.ami_id
  instance_type = "t3.medium"
}
```

The resource consists of:

```text
Resource
│
├── Provider
├── Resource Type
├── Resource Name
└── Arguments
```

Example:

```text
aws_instance
      │
      └── splunk
```

---

# 2.6 Data Sources

Data sources allow Terraform to retrieve information that already exists.

Example:

```hcl
data "aws_ami" "rhel" {
  most_recent = true

  owners = ["amazon"]
}
```

The difference is:

```text
Resource
   ↓
Create / Manage infrastructure

Data Source
   ↓
Read existing information
```

---

# 2.7 Variables

Variables allow Terraform configurations to accept configurable values.

Example:

```hcl
variable "region" {
  type    = string
  default = "ca-central-1"
}

variable "instance_type" {
  type    = string
  default = "t3.medium"
}
```

Variables help avoid hardcoding values throughout the configuration.

---

# 2.8 Locals

Locals define reusable values within a Terraform configuration.

Example:

```hcl
locals {
  environment = "lab"

  common_tags = {
    Environment = "lab"
    Project     = "splunk"
  }
}
```

Locals are useful for:

```text
Naming
Tags
Calculated Values
Reusable Expressions
```

---

# 2.9 Outputs

Outputs expose information from Terraform.

Example:

```hcl
output "splunk_instance_ip" {
  value = aws_instance.splunk.public_ip
}
```

After applying:

```bash
terraform output
```

Terraform can display:

```text
splunk_instance_ip = "203.0.113.10"
```

Outputs are commonly used to expose:

```text
IP Addresses
DNS Names
Instance IDs
Resource IDs
Network Information
```

---

# 2.10 Modules

Modules provide reusable Terraform configurations.

Example:

```text
modules/
└── splunk-instance/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

A module can be reused:

```text
Root Module
    │
    ├── Splunk Indexer Module
    ├── Splunk Search Head Module
    ├── Splunk Forwarder Module
    └── Network Module
```

Modules help reduce duplicated configuration.

---

# 2.11 State

Terraform state records information about infrastructure managed by Terraform.

Example:

```text
terraform.tfstate
```

Conceptually:

```text
Terraform Configuration
        │
        ▼
      State
        │
        ▼
Actual Infrastructure
```

Terraform uses state to understand:

```text
What Terraform manages
What resources exist
Resource IDs
Dependencies
Current attributes
Infrastructure relationships
```

State is an important part of Terraform's operation.

---

# 2.12 Backend

A backend determines where Terraform state is stored.

Example:

```text
Local Backend
      │
      ▼
terraform.tfstate
```

Remote backend:

```text
Terraform
    │
    ▼
 Remote Backend
    │
    ▼
Shared State
```

Remote state is commonly used for team environments.

---

# 2.13 Dependency Graph

Terraform builds a dependency graph to determine resource relationships and execution order.

Example:

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Security Group
 │
 ▼
EC2 Instance
```

Terraform can determine that the EC2 instance depends on other resources.

This allows Terraform to create resources in the appropriate order.

---

# 2.14 Plan

`terraform plan` creates an execution plan.

Example:

```bash
terraform plan
```

Terraform compares:

```text
Configuration
     │
     ▼
   State
     │
     ▼
Actual Infrastructure
```

It then determines the required changes.

Example:

```text
+ create
~ update
- destroy
-/+ replace
```

---

# 2.15 Apply

`terraform apply` applies the planned changes.

Example:

```bash
terraform apply
```

The general workflow is:

```text
Configuration
      │
      ▼
terraform plan
      │
      ▼
Execution Plan
      │
      ▼
terraform apply
      │
      ▼
Infrastructure
```

---

# 3. Architecture

## 3.1 Basic Architecture

Terraform commonly operates between configuration files and infrastructure APIs.

```text
                 Terraform
                     │
                     ▼
              Terraform Provider
                     │
                     ▼
                Cloud API
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
         VPC        EC2       Storage
```

---

# 3.2 Terraform Local Architecture

A basic Terraform project can look like:

```text
Terraform Project
│
├── Configuration
│
├── Terraform CLI
│
├── Provider
│
├── State
│
└── Backend
```

Execution:

```text
Terraform Configuration
          │
          ▼
      Terraform CLI
          │
          ▼
        Provider
          │
          ▼
       Cloud API
          │
          ▼
     Infrastructure
```

---

# 3.3 AWS Architecture

Terraform can provision AWS infrastructure.

Example:

```text
                    Terraform
                        │
                        ▼
                    AWS Provider
                        │
                        ▼
                     AWS API
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
         VPC          Security       EC2
                       Groups
                        │
                        ▼
                   Splunk Servers
```

---

# 3.4 Terraform Execution Model

A simplified Terraform execution process:

```text
Terraform Configuration
          │
          ▼
      terraform init
          │
          ▼
      Load Provider
          │
          ▼
      terraform plan
          │
          ▼
   Build Dependency Graph
          │
          ▼
    Compare Desired State
          │
          ▼
      Execution Plan
          │
          ▼
     terraform apply
          │
          ▼
      Provider API
          │
          ▼
     Infrastructure
          │
          ▼
       Update State
```

---

# 3.5 Terraform State Architecture

State connects Terraform configuration with real infrastructure.

```text
              Terraform
                  │
                  ▼
           Configuration
                  │
                  ▼
                State
                  │
                  ▼
          Actual Resources
```

With remote state:

```text
Developer 01 ──┐
               │
Developer 02 ──┼──► Remote Backend
               │
Developer 03 ──┘
```

This allows multiple administrators or automation systems to work with shared state.

---

# 3.6 Terraform and Ansible

Terraform and Ansible are often used together.

Terraform generally focuses on **infrastructure provisioning**.

Ansible generally focuses on **configuration and application management**.

Example:

```text
Terraform
   │
   ▼
Create AWS Infrastructure
   │
   ├── VPC
   ├── Subnet
   ├── Security Groups
   └── EC2 Instances
   │
   ▼
Infrastructure Exists
   │
   ▼
Ansible
   │
   ├── Configure OS
   ├── Install Packages
   ├── Configure Firewall
   ├── Install Splunk
   └── Configure Splunk
   │
   ▼
Application Ready
```

A common division is:

```text
Terraform = Provision Infrastructure

Ansible = Configure Infrastructure
```

The two tools can also overlap depending on the implementation.

---

# 3.7 Terraform Splunk Architecture

For a distributed Splunk environment, Terraform can provision the underlying infrastructure.

Example:

```text
                    Terraform
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
             VPC      Security    EC2
                       Groups
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
     Indexers        Search Heads     Forwarders
        │                │                │
      IDX01            SH01             UF01
      IDX02            SH02             UF02
      IDX03            SH03             UF02
```

Terraform can manage:

```text
VPC
Subnet
Security Groups
EC2 Instances
IAM
Storage
Networking
```

Ansible can then configure:

```text
Splunk Installation
Indexer Clustering
Search Head Clustering
Deployment Server
Universal Forwarders
SSL
Splunk Configuration
```

---

# 3.8 Multi-Environment Architecture

Terraform can manage multiple environments.

Example:

```text
                    Terraform
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
          Development   Test    Production
             │          │          │
             ▼          ▼          ▼
           AWS Lab    AWS Test   AWS Production
```

The infrastructure configuration can be reused while environment-specific values are supplied through variables, modules, or other configuration patterns.

---

# 4. Usage

## 4.1 Check Terraform Version

```bash
terraform version
```

---

# 4.2 Initialize Terraform

Initialize the working directory:

```bash
terraform init
```

This prepares Terraform and installs required providers and modules.

---

# 4.3 Format Configuration

Format Terraform files:

```bash
terraform fmt
```

Check formatting recursively:

```bash
terraform fmt -recursive
```

---

# 4.4 Validate Configuration

Validate the configuration:

```bash
terraform validate
```

This checks whether the configuration is syntactically and structurally valid.

---

# 4.5 Create an Execution Plan

```bash
terraform plan
```

Terraform shows what changes it intends to make.

Example:

```text
Plan:

+ create
~ update
- destroy
```

---

# 4.6 Apply Configuration

```bash
terraform apply
```

Terraform will display the execution plan and normally request confirmation.

Example:

```text
Do you want to perform these actions?

Enter a value:
yes
```

---

# 4.7 Automatic Approval

For automation environments:

```bash
terraform apply -auto-approve
```

This skips the interactive confirmation.

It should be used carefully, particularly against production infrastructure.

---

# 4.8 Destroy Infrastructure

Destroy resources managed by the configuration:

```bash
terraform destroy
```

Automatic approval:

```bash
terraform destroy -auto-approve
```

Example:

```text
Terraform
    │
    ▼
terraform destroy
    │
    ▼
Cloud Resources Removed
```

---

# 4.9 Show State

Display Terraform state:

```bash
terraform show
```

List managed resources:

```bash
terraform state list
```

---

# 4.10 Show Outputs

```bash
terraform output
```

Specific output:

```bash
terraform output splunk_instance_ip
```

---

# 4.11 Inspect Providers

Terraform can show provider requirements through the configuration.

Example:

```bash
terraform providers
```

This helps identify provider dependencies.

---

# 4.12 Terraform Console

Terraform provides an interactive console:

```bash
terraform console
```

Example:

```text
> var.region
"ca-central-1"
```

This can be useful for testing Terraform expressions.

---

# 4.13 Variables

Variables can be supplied through different mechanisms.

Example:

```hcl
variable "instance_type" {
  type    = string
  default = "t3.medium"
}
```

Terraform can receive values through:

```text
terraform.tfvars
*.auto.tfvars
Command-line variables
Environment variables
```

Example:

```bash
terraform apply -var="instance_type=t3.medium"
```

---

# 4.14 Count

`count` can create multiple instances of a resource.

Example:

```hcl
resource "aws_instance" "splunk" {
  count = 3

  ami           = var.ami_id
  instance_type = var.instance_type
}
```

This can create:

```text
splunk[0]
splunk[1]
splunk[2]
```

---

# 4.15 For Each

`for_each` can create resources based on a collection.

Example:

```hcl
variable "splunk_nodes" {
  default = {
    idx01 = "indexer"
    idx02 = "indexer"
    sh01  = "search_head"
  }
}
```

Then:

```hcl
resource "aws_instance" "splunk" {
  for_each = var.splunk_nodes

  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = each.key
    Role = each.value
  }
}
```

Terraform creates resources based on the supplied keys.

---

# 4.16 Dependencies

Terraform can determine implicit dependencies.

Example:

```hcl
resource "aws_subnet" "splunk" {
  vpc_id = aws_vpc.splunk.id
}
```

The dependency is:

```text
VPC
 │
 ▼
Subnet
```

Explicit dependencies can also be declared:

```hcl
depends_on = [
  aws_vpc.splunk
]
```

Dependencies help Terraform determine resource creation order.

---

# 4.17 Targeted Operations

Terraform provides commands for inspecting or working with individual resources.

Example:

```bash
terraform state show aws_instance.splunk
```

For normal infrastructure changes, it is generally preferable to let Terraform evaluate the complete configuration rather than relying heavily on targeted operations.

---

# 5. Configuration

## 5.1 Terraform Project Structure

A typical Terraform project can be organized as:

```text
terraform/
├── versions.tf
├── providers.tf
├── variables.tf
├── locals.tf
├── main.tf
├── outputs.tf
├── terraform.tfvars
├── modules/
│   ├── network/
│   └── splunk/
└── README.md
```

---

# 5.2 versions.tf

Provider and Terraform version requirements can be defined here.

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

Version constraints help make infrastructure deployments more predictable.

---

# 5.3 providers.tf

Example:

```hcl
provider "aws" {
  region = var.aws_region
}
```

The provider determines how Terraform communicates with AWS.

---

# 5.4 variables.tf

Variables are normally defined in:

```text
variables.tf
```

Example:

```hcl
variable "aws_region" {
  type        = string
  description = "AWS deployment region"
  default     = "ca-central-1"
}

variable "instance_type" {
  type        = string
  description = "EC2 instance type"
  default     = "t3.medium"
}
```

---

# 5.5 terraform.tfvars

Values can be supplied through:

```text
terraform.tfvars
```

Example:

```hcl
aws_region    = "ca-central-1"
instance_type = "t3.medium"
```

Sensitive values should not be committed to source control.

---

# 5.6 main.tf

The main infrastructure configuration can be stored in:

```text
main.tf
```

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

---

# 5.7 Outputs

Example:

```hcl
output "splunk_private_ip" {
  value = aws_instance.splunk.private_ip
}
```

Outputs can expose useful information after deployment.

---

# 5.8 Locals

Example:

```hcl
locals {
  common_tags = {
    Project     = "Splunk"
    Environment = "Lab"
    ManagedBy   = "Terraform"
  }
}
```

Resources can reuse the tags:

```hcl
tags = local.common_tags
```

---

# 5.9 Common Tags

Consistent tagging is important for cloud infrastructure.

Example:

```hcl
locals {
  common_tags = {
    Project     = "Splunk"
    Environment = "Lab"
    ManagedBy   = "Terraform"
    Owner       = "Infrastructure"
  }
}
```

Result:

```text
EC2
├── Project
├── Environment
├── ManagedBy
└── Owner
```

Tags can help with:

```text
Cost Management
Resource Identification
Automation
Operations
Inventory
Governance
```

---

# 5.10 Modules

Example:

```text
modules/
└── splunk/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

Use the module:

```hcl
module "splunk" {
  source = "./modules/splunk"

  instance_type = "t3.medium"
}
```

Modules allow reusable infrastructure patterns.

---

# 5.11 Backend Configuration

A backend can be configured for state management.

Conceptually:

```text
Terraform
    │
    ▼
Backend
    │
    ▼
Terraform State
```

A remote backend can provide centralized state storage and may support state locking depending on the backend.

---

# 5.12 State Security

Terraform state can contain sensitive infrastructure information.

Avoid storing state carelessly.

Example:

```text
Terraform State
│
├── Resource IDs
├── Network Information
├── Configuration Values
└── Potentially Sensitive Values
```

State should therefore be protected using appropriate:

```text
Access Controls
Encryption
Backend Security
Credential Management
State Locking
```

---

# 5.13 Secrets

Sensitive values should not be hardcoded.

Avoid:

```hcl
password = "MyPassword123"
```

Prefer appropriate secret-management mechanisms such as:

```text
Environment Variables
Cloud Secret Managers
Terraform Sensitive Variables
External Secret Systems
```

Mark Terraform variables as sensitive where appropriate:

```hcl
variable "password" {
  type      = string
  sensitive = true
}
```

Sensitive does not automatically mean that the value is absent from state, so state security remains important.

---

# 5.14 AWS Credentials

Terraform requires AWS authentication when managing AWS infrastructure.

Common approaches include:

```text
AWS CLI Configuration
Environment Variables
IAM Roles
Instance Roles
Federated Identity
Credential Management Systems
```

Avoid embedding long-lived access keys directly into Terraform configuration.

---

# 5.15 Lifecycle Configuration

Terraform resources can use lifecycle settings.

Example:

```hcl
resource "aws_instance" "splunk" {

  lifecycle {
    create_before_destroy = true
  }
}
```

Lifecycle controls can influence how Terraform handles resource changes.

Other lifecycle settings include:

```text
create_before_destroy
prevent_destroy
ignore_changes
replace_triggered_by
```

---

# 5.16 Resource Naming

Consistent resource naming makes infrastructure easier to manage.

Example:

```text
splunk-vpc
splunk-subnet
splunk-security-group
splunk-idx01
splunk-idx02
splunk-sh01
splunk-sh02
```

A consistent naming strategy helps with:

```text
Operations
Troubleshooting
Inventory
Cost Management
Automation
```

---

# 6. Dependencies

## 6.1 Terraform CLI

The Terraform CLI is required to execute Terraform configurations.

Verify:

```bash
terraform version
```

---

# 6.2 Operating System

Terraform can run on supported operating systems such as:

```text
Linux
Windows
macOS
```

The exact supported platforms depend on the Terraform release.

---

# 6.3 Provider

Terraform requires providers for the platforms it manages.

Example:

```text
Terraform
    │
    ▼
AWS Provider
    │
    ▼
AWS API
```

Without the required provider, Terraform cannot manage the corresponding resources.

---

# 6.4 Cloud Credentials

Cloud infrastructure requires authentication.

For AWS:

```text
Terraform
    │
    ▼
AWS Provider
    │
    ▼
AWS Credentials / IAM Role
    │
    ▼
AWS API
```

The authenticated identity must have the required permissions.

---

# 6.5 Network

Terraform requires network connectivity when communicating with remote provider APIs.

Example:

```text
Terraform Host
      │
      │ HTTPS
      ▼
Cloud Provider API
```

For AWS, Terraform commonly communicates with AWS APIs over HTTPS.

---

# 6.6 Cloud Service Limits

Cloud providers impose service quotas and limits.

Example:

```text
AWS Account
│
├── EC2 Limits
├── VPC Limits
├── EBS Limits
├── Elastic IP Limits
└── API Limits
```

Terraform cannot create resources beyond the limits imposed by the cloud provider.

For example:

```text
Terraform
    │
    ▼
AWS API
    │
    ▼
Service Quota
    │
    ├── Allowed → Create Resource
    │
    └── Exceeded → API Error
```

---

# 6.7 IAM Permissions

Terraform's cloud identity must have sufficient permissions.

Example:

```text
Terraform
    │
    ▼
IAM Identity
    │
    ▼
AWS API
    │
    ▼
Resources
```

Permissions may include access to:

```text
EC2
VPC
IAM
S3
EBS
CloudWatch
Security Groups
Load Balancers
```

The required permissions depend on the resources Terraform manages.

---

# 6.8 Backend

If using a remote backend, Terraform requires access to the backend.

Example:

```text
Terraform
    │
    ▼
Remote Backend
    │
    ▼
Terraform State
```

The backend may require:

```text
Authentication
Network Access
Permissions
State Locking
Encryption
```

---

# 6.9 Git

Git is not required for Terraform itself, but it is commonly used to manage Terraform code.

Example:

```text
Git Repository
│
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── modules/
└── README.md
```

Git provides:

* Version control
* Change history
* Collaboration
* Branching
* Rollback
* Code review

---

# 6.10 Ansible

Ansible is not a dependency of Terraform, but the two tools are frequently used together.

Example:

```text
Terraform
    │
    ▼
Infrastructure
    │
    ▼
Ansible
    │
    ▼
Configuration
```

Terraform can create:

```text
EC2
VPC
Subnet
Security Groups
```

Ansible can configure:

```text
Operating System
Packages
Firewall
Splunk
Applications
Services
```

---

# 6.11 CI/CD

Terraform can be integrated into CI/CD systems.

Example:

```text
Git
 │
 ▼
CI/CD Pipeline
 │
 ├── terraform fmt
 ├── terraform validate
 ├── terraform plan
 │
 ▼
Approval
 │
 ▼
terraform apply
 │
 ▼
Infrastructure
```

This allows infrastructure changes to be reviewed and automated.

---

# 7. License / Cost

## 7.1 Terraform Licensing

Terraform is distributed under HashiCorp's licensing terms, which have changed over time.

For production or organizational use, the **current Terraform licensing terms should be verified against HashiCorp's official documentation and the specific Terraform version being used**.

The important distinction is:

```text
Terraform Software
        │
        ▼
Licensing Terms

Infrastructure
        │
        ▼
Cloud Provider Costs
```

Terraform licensing and infrastructure costs are separate concerns.

---

# 7.2 Terraform Open-Source / Community Ecosystem

Terraform has a large ecosystem of providers, modules, and community resources.

Conceptually:

```text
Terraform
    │
    ├── Providers
    ├── Modules
    ├── Community Resources
    └── Enterprise Capabilities
```

The licensing terms of Terraform itself, providers, modules, and other components may differ and should be evaluated individually.

---

# 7.3 Terraform Enterprise / HCP Terraform

HashiCorp provides commercial offerings around Terraform, including **HCP Terraform** and other enterprise capabilities.

These offerings can provide capabilities around:

```text
Remote State
Team Collaboration
Policy
Governance
Access Control
Automation
Private Registry
Enterprise Workflows
```

The exact capabilities and pricing depend on the current HashiCorp offering and plan.

---

# 7.4 Terraform vs Infrastructure Cost

Terraform itself does not normally represent the primary cost of running cloud infrastructure.

Example:

```text
Terraform
    │
    ▼
Creates Infrastructure
    │
    ▼
Cloud Provider
    │
    ├── EC2
    ├── EBS
    ├── VPC
    ├── Load Balancer
    └── Storage
         │
         ▼
      Cloud Cost
```

For an AWS Splunk environment, costs may include:

```text
EC2 Instances
EBS Volumes
Data Transfer
Elastic IPs
Load Balancers
S3
CloudWatch
Other AWS Services
```

---

# 7.5 Splunk Infrastructure Cost

Terraform can provision infrastructure for Splunk, but Terraform does not eliminate the cost of the infrastructure or software running on it.

Example:

```text
Terraform
    │
    ▼
AWS Infrastructure
    │
    ├── EC2
    ├── EBS
    ├── Network
    └── Storage
    │
    ▼
Splunk Environment
```

Potential costs include:

```text
AWS Infrastructure
+
Splunk Licensing
+
Storage
+
Network
+
Administration
```

---

# 7.6 Operational Cost

Infrastructure as Code can reduce repetitive manual provisioning work.

Example:

```text
Manual:

Administrator
    │
    ├── Create VPC
    ├── Create Subnet
    ├── Create Security Group
    ├── Create EC2
    └── Configure Infrastructure


Terraform:

Administrator
       │
       ▼
 Terraform Code
       │
       ▼
 Infrastructure
```

However, Terraform automation itself requires:

* Development
* Testing
* Code review
* State management
* Security
* Documentation
* Maintenance
* Troubleshooting

---

# Quick Reference

## Core Components

| Component        | Purpose                                     |
| ---------------- | ------------------------------------------- |
| Terraform CLI    | Executes Terraform commands                 |
| HCL              | Defines Terraform configuration             |
| Provider         | Connects Terraform to external APIs         |
| Resource         | Represents infrastructure Terraform manages |
| Data Source      | Retrieves existing information              |
| Variable         | Provides configurable values                |
| Local            | Defines reusable local values               |
| Output           | Exposes Terraform values                    |
| Module           | Reusable Terraform configuration            |
| State            | Tracks managed infrastructure               |
| Backend          | Stores Terraform state                      |
| Dependency Graph | Determines resource relationships           |
| Plan             | Shows proposed infrastructure changes       |
| Apply            | Applies infrastructure changes              |

---

## Common Commands

```bash
# Check version
terraform version

# Initialize project
terraform init

# Format configuration
terraform fmt

# Format recursively
terraform fmt -recursive

# Validate configuration
terraform validate

# Create execution plan
terraform plan

# Apply configuration
terraform apply

# Apply without confirmation
terraform apply -auto-approve

# Destroy infrastructure
terraform destroy

# Show state
terraform show

# List managed resources
terraform state list

# Show specific resource state
terraform state show aws_instance.splunk

# Show outputs
terraform output

# Show providers
terraform providers

# Open Terraform console
terraform console
```

---

# Terraform Mental Model

The easiest way to understand Terraform is:

```text
                  TERRAFORM
                      │
                      ▼
                CONFIGURATION
                      │
                      ▼
                     HCL
                      │
                      ▼
                  PROVIDER
                      │
                      ▼
                  CLOUD API
                      │
                      ▼
                INFRASTRUCTURE
                      │
                      ▼
                    STATE
```

The fundamental infrastructure workflow is:

```text
DEFINE
   ↓
INITIALIZE
   ↓
VALIDATE
   ↓
PLAN
   ↓
APPLY
   ↓
MANAGE
   ↓
UPDATE / DESTROY
```

The Terraform execution model can be summarized as:

```text
Desired State
      ↓
Terraform Configuration
      ↓
Terraform Plan
      ↓
Provider
      ↓
Cloud API
      ↓
Actual Infrastructure
      ↓
Terraform State
```

For infrastructure engineering, the key concept is:

> **Define infrastructure as code, review the proposed changes, apply them consistently, and use state to manage the infrastructure lifecycle.**

For an AWS Splunk environment, this becomes:

```text
                    TERRAFORM
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
            VPC      NETWORKING   EC2
             │          │          │
             └──────────┼──────────┘
                        ▼
                Splunk Infrastructure
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       INDEXERS     SEARCH HEADS   FORWARDERS
          │             │             │
          ▼             ▼             ▼
        IDX01          SH01          UF01
        IDX02          SH02          UF02
        IDX03          SH03          UF02
```

Terraform is particularly useful when building a distributed Splunk environment because the underlying infrastructure can be defined consistently and recreated from code rather than manually provisioning each server and network component.

A common enterprise workflow is:

```text
                    TERRAFORM
                        │
                        ▼
              Provision Infrastructure
                        │
                        ▼
                 AWS Environment
                        │
                        ▼
                     ANSIBLE
                        │
                        ▼
              Configure Operating System
                        │
                        ▼
                 Install Splunk
                        │
                        ▼
              Configure Splunk Cluster
                        │
                        ▼
                Distributed Splunk
                  Environment
```

This creates a clear separation:

```text
Terraform
   ↓
Infrastructure

Ansible
   ↓
Configuration

Splunk
   ↓
Application / Observability Platform
```
