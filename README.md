# AWS Scalable 3-Tier Infrastructure (Terraform)

This repository demonstrates a production-grade, highly available 3-tier AWS architecture provisioned using **Terraform**. It follows the **Scalable & Self-Healing** pattern, a core requirement for the AWS Certified Solutions Architect - Associate (SAA-C03) exam.

## 🏗️ Architecture Overview

The infrastructure is designed to be resilient, secure, and automatically scalable. It consists of the following layers:

1.  **Public Tier:** An Application Load Balancer (ALB) distributed across two Availability Zones to handle incoming traffic.
2.  **Application Tier:** An Auto Scaling Group (ASG) using Launch Templates to maintain a self-healing pool of EC2 instances in private subnets.
3.  **Database Tier:** A Multi-AZ RDS MySQL instance providing automated failover and data redundancy across two zones.

## 🛠️ SAA-C03 Design Patterns Covered

- **Design Resilient Architectures (Domain 1):** Multi-AZ deployment for VPC subnets, ALB, and RDS ensures no single point of failure.
- **Design High-Performing Architectures (Domain 3):** Auto Scaling Groups provide horizontal elasticity based on demand.
- **Design Secure Architectures (Domain 2):** 
    - **Network Isolation:** Private subnets isolate the App and DB tiers from the internet.
    - **Security Group Chaining:** A "Least Privilege" model where traffic is restricted by source-group identity (ALB -> Web -> DB).

## 🚀 Technical Components

- **VPC & Networking:** Custom VPC with 6 subnets (2 Public, 4 Private), Internet Gateway, and explicit Route Table associations.
- **Compute:** EC2 Launch Templates and Auto Scaling Group for automated instance management.
- **Load Balancing:** Application Load Balancer with dynamic Target Group registration and health checks.
- **Database:** RDS Multi-AZ MySQL instance for managed relational data storage.
- **Infrastructure as Code:** 100% automated via Terraform with a state-managed lifecycle.

## 💻 Local Development

This project is optimized for testing using **LocalStack**.

### Prerequisites
- [Terraform](https://www.terraform.io/downloads)
- [Docker](https://www.docker.com/products/docker-desktop)
- [LocalStack](https://localstack.cloud/)

### Deployment
1. Start LocalStack: `docker-compose up -d`
2. Initialize Terraform: `terraform init`
3. Deploy Infrastructure: `terraform apply -auto-approve`

---

💡 **Pro Tip: Using `aws` instead of `awslocal`**

If you prefer using the standard `aws` CLI without the `awslocal` wrapper or repeating the `--endpoint-url` flag, you can configure a dedicated profile in your AWS config files.

### 1. Configure your Profile
Add the following to your `~/.aws/config` file:
```ini
[profile localstack]
region = us-east-1
output = json
# This line redirects all commands for this profile to LocalStack
endpoint_url = http://localhost:4566
```

Add matching dummy credentials to your `~/.aws/credentials` file:
```ini
[localstack]
aws_access_key_id = test
aws_secret_access_key = test
```

### 2. Use it in your Terminal
You can now run commands in two ways:

**Option A: Pass the profile flag**
```bash
aws iam create-user --user-name DevUser --profile localstack
```

**Option B: Set an environment variable (Recommended)**
Set your profile once in your session, and all subsequent `aws` commands will automatically target LocalStack:
```bash
export AWS_PROFILE=localstack
aws iam create-user --user-name DevUser
```

### Why this works
- **Precedence**: The AWS CLI (v2) supports a global `endpoint_url` setting within a profile. When this is set, the CLI automatically redirects all API calls for that profile to your local container instead of the real AWS cloud.
- **Convenience**: This allows you to use the standard documentation commands exactly as written, which is helpful if you are copy-pasting examples from AWS labs or tutorials.
