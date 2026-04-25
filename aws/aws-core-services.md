# AWS Core Services

## Overview
This document provides a simple and practical explanation of core AWS services, focusing on what they do and how they function in real-world scenarios.

---

## EC2 (Elastic Compute Cloud)

**What it is:**  
Virtual machines in the cloud.

**How it works:**
- Launch instances with CPU, RAM, and storage
- Connect via SSH (Linux) or RDP (Windows)
- Install and run applications like a traditional server

**Instance Types:**
- `t3.micro` – Low-cost, burstable (labs, small apps)
- `m6i.large` – General-purpose (balanced workloads)
- `c6i.large` – Compute-optimized (CPU-heavy tasks)
- `r6i.large` – Memory-optimized (databases, caching)

---

## VPC (Virtual Private Cloud)

**What it is:**  
Your isolated network inside AWS.

**How it works:**
- Define IP range using CIDR
- Create subnets:
  - Public (internet access)
  - Private (internal only)
- Control traffic using:
  - Security Groups (instance-level)
  - NACLs (subnet-level)

---

## S3 (Simple Storage Service)

**What it is:**  
Highly durable object storage.

**How it works:**
- Store data as objects inside buckets
- Accessible via HTTP/HTTPS
- Supports lifecycle policies and storage tiers

**Common Use Cases:**
- Backups
- Static websites
- Log storage (e.g., Splunk ingestion)

---

## RDS (Relational Database Service)

**What it is:**  
Managed relational database service.

**How it works:**
- AWS manages:
  - Backups
  - Updates and patching
  - Failover
- Supports:
  - MySQL, PostgreSQL, SQL Server, etc.

**Key Feature:**
- Multi-AZ for high availability

---

## IAM (Identity and Access Management)

**What it is:**  
Access control system for AWS.

**How it works:**
- Create:
  - Users
  - Roles
  - Policies
- Policies define permissions (JSON format)
- Roles allow services to securely access other services

**Best Practice:**
- Follow the principle of least privilege

---

## Route 53 (DNS Service)

**What it is:**  
Domain Name System (DNS) service.

**How it works:**
- Maps domain names to IP addresses
- Supports:
  - Health checks
  - Failover routing
  - Geo-based routing

---

## CloudWatch (Monitoring and Observability)

**What it is:**  
Monitoring and logging service.

**How it works:**
- Collects:
  - Metrics (CPU, memory, etc.)
  - Logs
- Set alarms based on thresholds
- Can trigger automated actions

---

## ELB (Elastic Load Balancing)

**What it is:**  
Distributes incoming traffic across multiple servers.

**How it works:**
- Routes traffic to multiple EC2 instances
- Improves:
  - Availability
  - Scalability

**Types:**
- ALB (Application Load Balancer – HTTP/HTTPS)
- NLB (Network Load Balancer – TCP/UDP)

---

## Lambda (Serverless Compute)

**What it is:**  
Run code without managing servers.

**How it works:**
- Triggered by events:
  - File upload (S3)
  - API calls
  - Scheduled events
- Automatically scales
- Pay per execution

---

## CloudFront (Content Delivery Network)

**What it is:**  
Global CDN for fast content delivery.

**How it works:**
- Caches content at edge locations worldwide
- Users access the nearest edge location to reduce latency

**Common Use Cases:**
- Static websites
- Media delivery
- API acceleration

---

## ECS (Elastic Container Service)

**What it is:**  
AWS-native container orchestration service.

**How it works:**
- Run Docker containers
- Define:
  - Tasks (container configuration)
  - Services (desired number of tasks)

**Launch Types:**
- EC2 (you manage instances)
- Fargate (serverless containers)

---

## EKS (Elastic Kubernetes Service)

**What it is:**  
Managed Kubernetes service.

**How it works:**
- AWS manages the control plane
- You manage worker nodes (or use Fargate)
- Ideal for Kubernetes-based workloads

---

## SQS (Simple Queue Service)

**What it is:**  
Message queue service for decoupling systems.

**How it works:**
- Producers send messages to a queue
- Consumers process messages asynchronously

**Benefits:**
- Improves reliability
- Enables scalable, decoupled architecture

---

## Summary

| Category     | Services                     |
|--------------|------------------------------|
| Compute      | EC2, Lambda                 |
| Networking   | VPC, Route 53, ELB          |
| Storage      | S3                          |
| Database     | RDS                         |
| Security     | IAM                         |
| Monitoring   | CloudWatch                  |
| Containers   | ECS, EKS                    |
| Integration  | SQS                         |
| Performance  | CloudFront                  |
