
---

# 🚀 Terraform Capstone Project: Automated WordPress Deployment on AWS

## 📘 Project Scenario

**DigitalBoost**, a digital marketing agency, aims to launch a high-performance WordPress website for clients. As an AWS Solutions Architect, your job is to automate the deployment using **Terraform** with a scalable, secure, and cost-effective infrastructure.

---

## 🎯 Project Objectives

- Create modular Terraform code for infrastructure provisioning.
- Deploy WordPress using Auto Scaling and Load Balancing.
- Store content using EFS and data using RDS.
- Use NAT Gateway for secure internet access in private subnets.
- Document security and architectural decisions.

---

## 🧱 Architecture Overview

### 📌 Infrastructure Components

- VPC with public and private subnets
- NAT Gateway
- Application Load Balancer (ALB)
- EC2 Instances in Auto Scaling Group
- Amazon RDS (MySQL)
- Amazon EFS (Elastic File System)
- Security Groups and IAM Roles

---

## 📁 Project Structure

```plaintext
terraform-wordpress/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── userdata/
│   └── wordpress.sh
├── modules/
│   ├── network/
│   ├── rds/
│   ├── efs/
│   ├── alb/
│   ├── autoscaling/
```

---

## 🔧 Module Breakdown

### 1. 🕸️ VPC Setup (`modules/network`)
- Create custom VPC
- Public & Private subnets
- Route tables
- Internet Gateway
- NAT Gateway for private subnet access

### 2. 🗃️ RDS Setup (`modules/rds`)
- MySQL engine
- Multi-AZ (optional)
- Subnet Group
- DB Security Group

### 3. 🗂️ EFS Setup (`modules/efs`)
- Shared storage for WordPress files
- Mount targets in subnets
- Security Group to allow NFS traffic

### 4. ⚖️ Application Load Balancer (`modules/alb`)
- ALB with listener on port 80
- Target group for Auto Scaling Group
- Health checks and public DNS output

### 5. 📈 Auto Scaling Group (`modules/autoscaling`)
- Launch Template with EC2 + UserData
- WordPress installation via script
- EFS mount and connection to RDS
- Auto Scaling Group with policies

---

## 🛠️ UserData Script (`userdata/wordpress.sh`)

```bash
#!/bin/bash
yum update -y
yum install -y httpd php php-mysqlnd mysql unzip amazon-efs-utils
systemctl start httpd
systemctl enable httpd

# Mount EFS
mkdir /var/www/html
mount -t efs -o tls ${efs_id}:/ /var/www/html

# Download and install WordPress
cd /tmp
wget https://wordpress.org/latest.zip
unzip latest.zip
cp -r wordpress/* /var/www/html/

# Set permissions
chown -R apache:apache /var/www/html
chmod -R 755 /var/www/html

# Configure WordPress
cat <<EOF > /var/www/html/wp-config.php
<?php
define('DB_NAME', '${db_name}');
define('DB_USER', '${db_user}');
define('DB_PASSWORD', '${db_password}');
define('DB_HOST', '${db_endpoint}');
define('DB_CHARSET', 'utf8');
define('DB_COLLATE', '');
\$table_prefix = 'wp_';
define('WP_DEBUG', false);
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__) . '/');
require_once(ABSPATH . 'wp-settings.php');
?>
EOF
```

---

## ⚙️ Terraform Commands

```bash
terraform init        # Initialize the working directory
terraform plan        # Preview the infrastructure changes
terraform apply       # Apply the configuration to deploy
terraform destroy     # Tear down the infrastructure (optional)
```

---

## 🔐 Security Measures

- EC2 instances in private subnet with no public IPs.
- NAT Gateway for outbound internet access (private subnet).
- Load Balancer in public subnet handles inbound traffic.
- Security Groups with restricted ports (SSH, HTTP, MySQL).
- IAM Roles to control EC2 access to EFS and other services.

---

## ✅ Deliverables

- [x] Working WordPress site behind Load Balancer.
- [x] RDS for secure database storage.
- [x] EFS for shared and persistent content.
- [x] Terraform Modules for all components.
- [x] Documentation with explanation and code structure.

---

## 🧪 Testing Auto Scaling (Demo)

To simulate traffic and test auto scaling:

```bash
sudo apt install apache2-utils
ab -n 1000 -c 100 http://<alb-dns-name>/
```

Monitor the EC2 instance count and ALB metrics in AWS Console.

---
