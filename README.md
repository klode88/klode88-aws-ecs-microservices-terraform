# AWS ECS Microservices with Terraform

## Project Overview

This project demonstrates how to deploy a **containerized microservices architecture on AWS** using **Terraform, Docker, and Amazon ECS Fargate**.

The application simulates a simplified **online marketplace platform** composed of two independent microservices:

* **Product Service** – handles product catalogue requests
* **Cart Service** – manages shopping cart operations

Both services run as Docker containers deployed on **AWS ECS Fargate** and are accessed through a single **Application Load Balancer (ALB)**.

All infrastructure is provisioned using **Terraform Infrastructure as Code (IaC)**.

---

# Architecture Diagram

```
                    Users
                      │
                      ▼
        ┌───────────────────────────┐
        │   Application Load Balancer │
        └───────────────┬───────────┘
                        │
           ┌────────────┴────────────┐
           │                         │
           ▼                         ▼
   Product Service             Cart Service
   (ECS Fargate)               (ECS Fargate)
           │                         │
           └──────────┬──────────────┘
                      │
              Private Subnets
                      │
                      ▼
               NAT Gateway
                (Elastic IP)
                      │
                      ▼
                   Internet

Docker Images stored in:
Amazon Elastic Container Registry (ECR)
```

---

# Architecture Overview

The application runs inside a custom **Amazon VPC** using a secure multi-tier architecture.

### Core AWS Services Used

* Amazon VPC
* Public Subnets
* Private Subnets
* Internet Gateway
* NAT Gateway with Elastic IP
* Application Load Balancer (ALB)
* Amazon ECS (Fargate)
* Amazon ECR (Container Registry)
* Amazon CloudWatch Logs
* AWS Cloud Map (Service Discovery)
* Terraform Infrastructure as Code

---

# Microservices

## Product Service

The **Product Service** handles product catalogue requests.

Example endpoint:

```
http://ALB-DNS/
```

Response:

```
Product Service Running
```

---

## Cart Service

The **Cart Service** handles shopping cart functionality.

Example endpoint:

```
http://ALB-DNS/cart
```

Response:

```
Cart Service Running
```

---

# System Traffic Flow

The system processes user requests using the following flow:

1. A user sends a request to the **Application Load Balancer (ALB)**.
2. The ALB evaluates **listener routing rules**.
3. Requests to `/` are routed to the **Product Service**.
4. Requests to `/cart` are routed to the **Cart Service**.
5. The ALB forwards traffic to the appropriate **Target Group**.
6. The Target Group sends traffic to the **ECS Service running on Fargate**.
7. ECS launches containers from images stored in **Amazon ECR**.
8. The container processes the request and sends the response back through the ALB.

---

# Networking Architecture

The infrastructure uses a **public/private subnet architecture**.

### Public Layer

* Application Load Balancer
* Internet Gateway

### Private Layer

* ECS Fargate containers
* Application services

Containers run inside **private subnets** and are not directly accessible from the internet.

Outbound internet access is handled through a **NAT Gateway**.

---

# Why the NAT Gateway Uses an Elastic IP

The NAT Gateway requires an **Elastic IP** because it must provide a **stable public IP address** for outbound internet traffic.

This allows private ECS containers to:

* Pull Docker images from Amazon ECR
* Access external APIs
* Install updates

Without exposing the containers directly to the internet.

This architecture improves **security, scalability, and network isolation**.

---

# Infrastructure Deployment

All infrastructure in this project is provisioned using **Terraform**.

Terraform creates:

* VPC network
* Public and private subnets
* Internet Gateway
* NAT Gateway with Elastic IP
* Security groups
* Application Load Balancer
* Target groups
* ECS cluster
* ECS services
* ECR repositories
* CloudWatch log groups
* Service discovery

---

# Deployment Walkthrough

Below is the step-by-step process used to deploy the system.

---

## 1. Development Environment Setup

Terraform and AWS CLI were configured locally.

![Setup](screenshots/01_SETTING_UP_ENVIRONMENT.png)

---

## 2. Terraform Initialization

Terraform providers and backend were initialized.

![Terraform Init](screenshots/02_BACKEND_INITIALIZED.png)

---

## 3. Terraform Plan

Terraform plan previewed the infrastructure before deployment.

![Terraform Plan](screenshots/02_TERRAFORM_PLAN.png)

---

## 4. Terraform Apply

Infrastructure was deployed using Terraform.

![Terraform Apply](screenshots/03_TERRAFORM_APPLY.png)

---

## 5. IAM Permission Issue

An IAM permission issue occurred during deployment.

![IAM Error](screenshots/05_IAM_USER_ERROR.png)

---

## 6. IAM Permissions Added

Additional permissions were granted to resolve the issue.

![IAM Fix](screenshots/06_PERMISSIONS_ADDED.png)

---

## 7. VPC Created

The VPC provides the networking foundation for the application.

![VPC](screenshots/07_VPC_CREATED.png)

---

## 8. Internet Gateway Created

The Internet Gateway enables internet access for public resources.

![Internet Gateway](screenshots/09_IG_CREATED.png)

---

## 9. Public and Private Subnets

Subnets were created across multiple availability zones.

![Subnets](screenshots/10_PRIVATE_PUBLIC_SUBNETS.png)

---

## 10. Multi-AZ Subnets

Subnets were distributed across multiple AZs.

![Multi AZ](screenshots/11_SUBNETS_ACROSS_AZ.png)

---

## 11. Route Tables

Routing tables were configured for internet connectivity.

![Route Tables](screenshots/12_PUBLIC_ROUTE_TABLE.png)

---

## 12. NAT Gateway Created

A NAT Gateway enables outbound internet access for private containers.

![NAT Gateway](screenshots/13_NAT_CREATED.png)

---

## 13. Application Load Balancer Created

The ALB acts as the public entry point for the system.

![ALB](screenshots/14_ALB_CREATED.png)

---

## 14. ALB Listener and Target Groups

Listener rules route traffic to the correct microservice.

![ALB Routing](screenshots/15_ALB_LISTENER_TARGET_GROUP.png)

---

## 15. ECS Cluster Created

An ECS cluster was created to host containerized services.

![ECS Cluster](screenshots/16_ECS_CLUSTER.png)

---

## 16. ECR Repositories Created

Docker images are stored in Amazon ECR.

![ECR](screenshots/17_ECR_REPOS_CREATED.png)

---

## 17. Product Service Running

The Product Service container was deployed successfully.

![Product Service](screenshots/18_PRODUCT_SERVICE_RUNNING.png)

---

## 18. Cart Service Running

The Cart Service container was deployed successfully.

![Cart Service](screenshots/19_CART_SERVICE_RUNNING.png)

---

# Technologies Used

* AWS ECS (Fargate)
* AWS ECR
* AWS VPC
* AWS Application Load Balancer
* AWS CloudWatch
* AWS Cloud Map
* Terraform
* Docker
* Python Flask

---

# Repository Structure

```
aws-ecs-microservices-terraform
│
├── cart-service
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
│
├── product-service
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
│
├── terraform
│   ├── main.tf
│   ├── provider.tf
│   └── versions.tf
│
├── screenshots
│
└── README.md
```

---

# Cleanup

To remove all AWS resources created by this project:

```
terraform destroy
```

This command deletes all infrastructure created by Terraform.

