
# 🖥️ Mini Project: EC2 Module and Security Group Module with Apache2 UserData

---

## 🎯 Purpose

This project showcases how to modularize Terraform configurations to:
- Deploy an **EC2 instance**
- Create a **Security Group**
- Install **Apache2** using a **UserData script**

---

## 🧠 Objectives

### ✅ Terraform Module Creation
- Learn how to structure reusable Terraform modules for EC2 and Security Groups

### ✅ EC2 Instance Configuration
- Deploy an EC2 instance with custom input values

### ✅ Security Group Configuration
- Configure an isolated and reusable security group module

### ✅ UserData Script
- Automatically install Apache2 and set up a simple HTML page at launch

---

## 🛠 Project Tasks

---

### 📁 Task 1: EC2 Module

1. **Create project structure:**

   ```bash
   mkdir -p terraform-ec2-apache/modules/ec2
   ```

2. **`modules/ec2/main.tf`:**

   ```hcl
   variable "ami_id" {}
   variable "instance_type" {}
   variable "key_name" {}
   variable "security_group_id" {}
   variable "user_data" {}

   resource "aws_instance" "web" {
     ami                    = var.ami_id
     instance_type          = var.instance_type
     key_name               = var.key_name
     security_groups        = [var.security_group_id]
     user_data              = var.user_data

     tags = {
       Name = "Apache-Web-Server"
     }
   }

   output "public_ip" {
     value = aws_instance.web.public_ip
   }

   output "public_dns" {
     value = aws_instance.web.public_dns
   }
   ```

---

### 🛡️ Task 2: Security Group Module

1. **Create the SG module:**

   ```bash
   mkdir -p terraform-ec2-apache/modules/security_group
   ```

2. **`modules/security_group/main.tf`:**

   ```hcl
   variable "vpc_id" {}

   resource "aws_security_group" "web_sg" {
     name        = "apache-sg"
     description = "Allow HTTP and SSH"
     vpc_id      = var.vpc_id

     ingress {
       from_port   = 22
       to_port     = 22
       protocol    = "tcp"
       cidr_blocks = ["0.0.0.0/0"]
     }

     ingress {
       from_port   = 80
       to_port     = 80
       protocol    = "tcp"
       cidr_blocks = ["0.0.0.0/0"]
     }

     egress {
       from_port   = 0
       to_port     = 0
       protocol    = "-1"
       cidr_blocks = ["0.0.0.0/0"]
     }

     tags = {
       Name = "apache-sg"
     }
   }

   output "security_group_id" {
     value = aws_security_group.web_sg.id
   }
   ```

---

### 📜 Task 3: UserData Script

1. **Create and save the file as `apache_userdata.sh`:**

   ```bash
   nano apache_userdata.sh
   ```

   ```bash
   #!/bin/bash
   sudo yum update -y
   sudo yum install -y httpd
   sudo systemctl start httpd
   sudo systemctl enable httpd
   echo "<h1>Hello World from $(hostname -f)</h1>" | sudo tee /var/www/html/index.html
   ```

2. **Make it executable:**

   ```bash
   chmod +x apache_userdata.sh
   ```

---

### 📦 Task 4: Main Terraform Configuration

1. **Create `main.tf`:**

   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   data "aws_vpc" "default" {
     default = true
   }

   module "security_group" {
     source  = "./modules/security_group"
     vpc_id  = data.aws_vpc.default.id
   }

   module "ec2_instance" {
     source            = "./modules/ec2"
     ami_id            = "ami-0c02fb55956c7d316" # Amazon Linux 2 AMI
     instance_type     = "t2.micro"
     key_name          = "your-key-name"
     security_group_id = module.security_group.security_group_id
     user_data         = file("apache_userdata.sh")
   }

   output "ec2_public_ip" {
     value = module.ec2_instance.public_ip
   }

   output "ec2_public_dns" {
     value = module.ec2_instance.public_dns
   }
   ```

---

### 🚀 Task 5: Deployment

1. **Initialize Terraform and deploy:**

   ```bash
   terraform init
   terraform apply
   ```

2. **Access the Apache Server:**

   Visit the public IP or DNS printed in the output:
   ```
   http://<public_ip>
   ```

---

## 📌 Side Notes

- Make sure AWS CLI is configured: `aws configure`
- Replace `your-key-name` with your actual EC2 key pair
- Use correct region and AMI ID
- Default VPC is used here for simplicity

---

## 🔍 Observations

- The modular setup makes it easy to reuse EC2 and SG components
- Apache installation via UserData is effective and automates provisioning
- Outputs help verify the instance and access details

---

## ⚠️ Challenges Faced

- Need to ensure EC2 has proper key and security group access
- Some regions may not support the default AMI ID provided
- Waiting for EC2 provisioning can take time

---

## ✅ Outcome

✅ EC2 instance deployed  
✅ Apache2 installed using UserData  
✅ Security Group allows HTTP and SSH access  
✅ Web page viewable from browser  

---
