# AWS VPC Infrastructure with EC2, Application Load Balancer and S3 using Terraform

## Project Overview

This project demonstrates how to provision a complete AWS infrastructure using Terraform.

The infrastructure includes:

* Custom Virtual Private Cloud (VPC)
* Public Subnets across multiple Availability Zones
* Internet Gateway
* Route Tables
* Security Groups
* EC2 Web Servers
* Application Load Balancer (ALB)
* Target Groups
* Listener Configuration
* Amazon S3 Bucket

The entire infrastructure is deployed using Infrastructure as Code (IaC) principles through Terraform.

---

# Architecture

```text
                         Internet
                             │
                             ▼
                  Application Load Balancer
                             │
                  ┌──────────┴──────────┐
                  │                     │
                  ▼                     ▼

           EC2 Web Server 1      EC2 Web Server 2
           (Subnet us-east-1a)   (Subnet us-east-1b)

                  │                     │
                  └──────────┬──────────┘
                             │
                             ▼

                         Custom VPC

                             │
                             ▼

                        S3 Bucket
```

---

# Project Objectives

This project was built to learn:

* Terraform Fundamentals
* Infrastructure as Code (IaC)
* AWS Networking
* VPC Configuration
* Public Subnet Design
* Security Groups
* EC2 Instance Deployment
* User Data Scripts
* Application Load Balancer
* Target Groups and Health Checks
* S3 Bucket Creation
* Terraform State Management

---

# AWS Services Used

| Service          | Purpose                     |
| ---------------- | --------------------------- |
| VPC              | Isolated network            |
| Subnet           | Host EC2 instances          |
| Internet Gateway | Internet access             |
| Route Table      | Route traffic               |
| Security Group   | Firewall rules              |
| EC2              | Web servers                 |
| ALB              | Load balancing              |
| Target Group     | Backend server registration |
| S3               | Object storage              |
| IAM              | AWS Authentication          |

---

# Infrastructure Components

## VPC

Creates a custom VPC:

```hcl
resource "aws_vpc" "myvpc" {
  cidr_block = "10.0.0.0/16"
}
```

CIDR Range:

```text
10.0.0.0/16
```

---

## Public Subnets

Two public subnets are created:

| Subnet  | Availability Zone |
| ------- | ----------------- |
| subnet1 | us-east-1a        |
| subnet2 | us-east-1b        |

Both subnets automatically assign public IPs.

---

## Internet Gateway

Provides internet connectivity to resources inside the VPC.

```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.myvpc.id
}
```

---

## Route Table

Allows outbound internet access.

```hcl
route {
  cidr_block = "0.0.0.0/0"
  gateway_id = aws_internet_gateway.igw.id
}
```

---

## Security Group

Inbound Rules:

| Port | Protocol | Purpose |
| ---- | -------- | ------- |
| 22   | TCP      | SSH     |
| 80   | TCP      | HTTP    |

Outbound:

```text
All Traffic Allowed
```

---

## EC2 Instances

Two Ubuntu EC2 instances are deployed.

Instance Type:

```text
t2.micro
```

AMI:

```text
Ubuntu 20.04 LTS
```

---

## User Data Scripts

Web Server 1:

```bash
#!/bin/bash
apt update -y
apt install apache2 -y
systemctl start apache2
systemctl enable apache2
echo "<h1>Web Server 1</h1>" > /var/www/html/index.html
```

Web Server 2:

```bash
#!/bin/bash
apt update -y
apt install apache2 -y
systemctl start apache2
systemctl enable apache2
echo "<h1>Web Server 2</h1>" > /var/www/html/index.html
```

---

## Application Load Balancer

The ALB distributes incoming traffic across both EC2 instances.

Configuration:

```text
Type: Application Load Balancer
Scheme: Internet Facing
Protocol: HTTP
Port: 80
```

---

## Target Group

Backend EC2 instances are registered inside the target group.

Health Check:

```text
Path: /
Port: traffic-port
Protocol: HTTP
```

---

## Listener

The listener forwards requests from:

```text
Port 80
```

to:

```text
Target Group
```

---

## Amazon S3 Bucket

Terraform creates an S3 bucket for storage.

Example:

```hcl
resource "aws_s3_bucket" "example" {
  bucket = "your-unique-bucket-name"
}
```

Note:

S3 bucket names must be globally unique.

---

# Project Structure

```text
vpc_with_ec2/
│
├── main.tf
├── variables.tf
├── userdata.sh
├── userdata1.sh
├── .gitignore
├── .terraform.lock.hcl
└── README.md
```

---

# Prerequisites

Before running this project:

## Terraform

Verify installation:

```bash
terraform version
```

---

## AWS CLI

Verify installation:

```bash
aws --version
```

---

## Configure AWS Credentials

```bash
aws configure
```

Provide:

```text
AWS Access Key ID
AWS Secret Access Key
Default Region: us-east-1
Output Format: json
```

---

# Deployment Steps

## Initialize Terraform

```bash
terraform init
```

---

## Validate Configuration

```bash
terraform validate
```

---

## Format Terraform Files

```bash
terraform fmt
```

---

## Preview Infrastructure

```bash
terraform plan
```

---

## Deploy Infrastructure

```bash
terraform apply
```

or

```bash
terraform apply -auto-approve
```

---

# Testing

After deployment:

```bash
terraform output
```

Example:

```text
loadbalancerdns = myalb-xxxxxxxx.us-east-1.elb.amazonaws.com
```

Open in browser:

```text
http://myalb-xxxxxxxx.us-east-1.elb.amazonaws.com
```

Expected Output:

```html
Web Server 1
```

or

```html
Web Server 2
```

---

# Troubleshooting

## Error: BucketAlreadyExists

Cause:

```text
S3 bucket names are globally unique.
```

Solution:

Use a unique bucket name.

---

## Error: Invalid AWS Region

Cause:

```text
AWS CLI region not configured.
```

Solution:

```bash
aws configure
```

---

## Error: Target Group Unhealthy

Cause:

Incorrect web server installation.

For Ubuntu use:

```bash
apt install apache2 -y
```

instead of:

```bash
yum install httpd -y
```

---

## Error: GitHub Push Failed

Cause:

Terraform provider files committed accidentally.

Solution:

Create `.gitignore`:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
terraform.tfvars
terraform.tfvars.json
crash.log
```

---

# Terraform Commands Used

```bash
terraform init
terraform validate
terraform fmt
terraform plan
terraform apply
terraform show
terraform state list
terraform destroy
```

---

# Learning Outcomes

After completing this project, you will understand:

* Terraform Basics
* Infrastructure as Code
* AWS Networking
* VPC Design
* Public Subnets
* Route Tables
* Internet Gateway
* Security Groups
* EC2 Deployment
* User Data Scripts
* Application Load Balancers
* Target Groups
* Health Checks
* Amazon S3
* Terraform State Management

---

# Future Improvements

Possible enhancements:

* Auto Scaling Group
* Launch Templates
* NAT Gateway
* Private Subnets
* RDS Database
* Route 53
* HTTPS using ACM
* Remote State Backend (S3)
* Terraform Modules
* CI/CD with GitHub Actions

---

## What We Did in This Project
# What We Did in This Project

* Configured AWS CLI and authenticated with AWS account.
* Created Terraform configuration files for infrastructure provisioning.
* Defined a custom VPC with CIDR block `10.0.0.0/16`.
* Created two public subnets in different Availability Zones (`us-east-1a` and `us-east-1b`).
* Enabled automatic public IP assignment for instances launched in public subnets.
* Created and attached an Internet Gateway to the VPC.
* Created a Route Table and configured internet access using `0.0.0.0/0`.
* Associated the Route Table with both public subnets.
* Created a Security Group to allow HTTP (80) and SSH (22) traffic.
* Created an Amazon S3 Bucket using Terraform.
* Created User Data scripts for automatic server configuration.
* Installed Apache Web Server on EC2 instances using User Data.
* Created two Ubuntu EC2 instances in separate Availability Zones.
* Configured Web Server 1 to display "Web Server 1".
* Configured Web Server 2 to display "Web Server 2".
* Created an Application Load Balancer (ALB).
* Configured the ALB as an internet-facing load balancer.
* Created a Target Group for backend EC2 instances.
* Registered both EC2 instances with the Target Group.
* Configured health checks for the Target Group.
* Created a Listener on Port 80.
* Configured Listener rules to forward traffic to the Target Group.
* Initialized Terraform providers using `terraform init`.
* Validated Terraform configuration using `terraform validate`.
* Reviewed infrastructure changes using `terraform plan`.
* Deployed infrastructure using `terraform apply`.
* Verified successful creation of all AWS resources.
* Tested the Load Balancer DNS endpoint.
* Verified traffic routing between multiple EC2 instances.
* Confirmed successful health checks for both targets.
* Resolved S3 bucket naming conflicts.
* Resolved Terraform variable configuration issues.
* Resolved User Data script file errors.
* Resolved Ubuntu Apache installation issues (`apt` instead of `yum`).
* Resolved Target Group unhealthy status.
* Resolved ALB 502 Bad Gateway errors.
* Implemented Infrastructure as Code (IaC) using Terraform.
* Learned AWS networking concepts including VPCs, Subnets, Route Tables, and Internet Gateways.
* Learned AWS Load Balancing concepts using Application Load Balancer.
* Learned Terraform state management and deployment workflow.
* Learned Git and GitHub best practices for Terraform projects.
* Implemented `.gitignore` to prevent Terraform-generated files from being pushed to GitHub.
* Successfully deployed a production-style AWS infrastructure using Terraform.





# Author

Om Yengantiwar

