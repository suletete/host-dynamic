
# 🚀 Mini Project: Hosting a Dynamic Web App on AWS with Terraform Modules, Docker, Amazon ECR, and ECS

---

## 🎯 Purpose

This mini project demonstrates how to use **Terraform**, **Docker**, **Amazon ECR**, and **Amazon ECS** to:
- Containerize a dynamic web app
- Push the image to a private container registry (ECR)
- Deploy the containerized app on AWS ECS using a modular Terraform setup

---

## 🧠 Objectives

### ✅ Terraform Module Creation
- Build reusable modules for ECR and ECS provisioning

### ✅ Dockerization
- Containerize a dynamic web application (Node.js, Flask, Django, etc.)

### ✅ Amazon ECR Configuration
- Provision ECR using Terraform and push Docker images

### ✅ Amazon ECS Deployment
- Use Terraform to spin up ECS cluster and deploy the app

---

## 🛠 Project Tasks

---

### Task 1: Dockerization of Web App

1. **Create your dynamic web app** (example: Flask, Node.js).

2. **Create a `Dockerfile`** in the root of your app:

   ```Dockerfile
   FROM python:3.10-slim

   WORKDIR /app

   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt

   COPY . .

   CMD ["python", "app.py"]
   ```

3. **Test the image locally:**

   ```bash
   docker build -t mywebapp .
   docker run -p 5000:5000 mywebapp
   ```

---

### Task 2: Terraform Module for Amazon ECR

1. **Create the ECR module:**

   ```bash
   mkdir -p terraform-ecs-webapp/modules/ecr
   ```

2. **ECR module - `modules/ecr/main.tf`:**

   ```hcl
   variable "repository_name" {}

   resource "aws_ecr_repository" "webapp_repo" {
     name = var.repository_name
   }

   output "repository_url" {
     value = aws_ecr_repository.webapp_repo.repository_url
   }
   ```

---

### Task 3: Terraform Module for ECS

1. **Create the ECS module:**

   ```bash
   mkdir -p modules/ecs
   ```

2. **ECS module - `modules/ecs/main.tf`:**

   *(Example ECS Fargate deployment – customize as needed)*

   ```hcl
   variable "ecr_repository_url" {}
   variable "container_name" { default = "webapp" }
   variable "cpu" { default = "256" }
   variable "memory" { default = "512" }

   resource "aws_ecs_cluster" "cluster" {
     name = "webapp-cluster"
   }

   resource "aws_ecs_task_definition" "task" {
     family                   = "webapp-task"
     network_mode             = "awsvpc"
     requires_compatibilities = ["FARGATE"]
     cpu                      = var.cpu
     memory                   = var.memory
     execution_role_arn       = aws_iam_role.ecs_execution_role.arn

     container_definitions = jsonencode([
       {
         name      = var.container_name,
         image     = var.ecr_repository_url,
         essential = true,
         portMappings = [
           {
             containerPort = 5000,
             hostPort      = 5000,
           }
         ]
       }
     ])
   }

   # Define ECS service, roles, security groups, etc. as needed...
   ```

---

### Task 4: Main Terraform Configuration

1. **Create `main.tf` in your project root:**

   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   module "ecr" {
     source           = "./modules/ecr"
     repository_name  = "webapp-repo"
   }

   module "ecs" {
     source             = "./modules/ecs"
     ecr_repository_url = module.ecr.repository_url
     container_name     = "webapp"
   }
   ```

---

### Task 5: Deployment

1. **Build and tag your Docker image:**

   ```bash
   docker build -t webapp .
   ```

2. **Login to ECR:**

   ```bash
   aws ecr get-login-password --region us-east-1 | \
     docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.us-east-1.amazonaws.com
   ```

3. **Tag and push the image:**

   ```bash
   docker tag webapp:latest <repo-url>:latest
   docker push <repo-url>:latest
   ```

4. **Initialize and deploy infrastructure:**

   ```bash
   terraform init
   terraform apply
   ```

5. **Access the deployed web app** via the public IP or DNS of the ECS service (from ALB or ECS output).

---

## 📌 Side Notes

- Ensure the **AWS CLI** is installed and authenticated.
- Replace placeholders like `<repo-url>`, `<your-account-id>` with actual values.
- If using **Fargate**, set up:
  - Execution IAM roles
  - Subnets and Security Groups
  - Load balancer (optional but useful)
- Include `variables.tf` and `outputs.tf` in modules for better reusability.

---

## 🔍 Observations

- Terraform effectively modularized both ECR and ECS setup.
- Pushing to ECR and referencing the repo in ECS made the deployment smooth.
- Container definition within the ECS task must match port and memory limits.

---

## ⚠️ Challenges Faced

- **IAM permissions** are crucial. ECS tasks require execution roles to pull from ECR.
- **Networking setup** for ECS (especially for Fargate) can be complex.
- Docker image must be pushed **before ECS deployment** or the task will fail.

---

## ✅ Outcome

✅ Dynamic web app containerized  
✅ Image stored in ECR  
✅ ECS Cluster deployed via Terraform  
✅ App accessible from browser  

---
