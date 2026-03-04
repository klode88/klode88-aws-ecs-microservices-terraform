# klode88-aws-ecs-microservices-terraform
# Online Marketplace Microservices (AWS + Terraform + ECS Fargate)

## Project Overview

This project demonstrates how to deploy a containerized microservices architecture on AWS using Terraform.

The system simulates an online marketplace composed of two microservices:

- Product Service – handles product catalogue requests
- Cart Service – manages shopping cart operations

Both services run as Docker containers on AWS ECS Fargate behind an Application Load Balancer.

Infrastructure is fully provisioned using Terraform.

---

## Architecture Overview

Main AWS services used:

- Amazon VPC
- Public and Private Subnets
- Internet Gateway
- NAT Gateway with Elastic IP
- Application Load Balancer
- Amazon ECS (Fargate)
- Amazon ECR
- CloudWatch Logs
- AWS Cloud Map (Service Discovery)
- Terraform Infrastructure as Code

---

## System Architecture

User traffic flows through an Application Load Balancer which routes requests to the correct microservice based on URL paths.

Example routing:

/        → Product Service  
/cart    → Cart Service

Containers run in private subnets and access the internet through a NAT Gateway.

---

## Real Business Scenario

In a real online marketplace platform:

- The **Product Service** handles product listings, search, and details.
- The **Cart Service** manages user shopping carts.

Separating these into microservices allows independent scaling, deployment, and development by different teams.

For example:

- High traffic on checkout pages can scale the Cart Service independently.
- Product catalogue updates can be deployed without affecting the cart system.

---

## Infrastructure Deployment

Infrastructure is provisioned using Terraform.

Key components created:

- VPC network with multiple subnets
- Internet and NAT gateways
- Application Load Balancer
- ECS cluster with Fargate services
- Container registry using Amazon ECR
- Logging using CloudWatch

---

## Deployment Walkthrough

Below is the step-by-step deployment process.
### 1. Setting Up the Development Environment

Terraform and AWS CLI were configured locally to interact with AWS.

![Setup Environment](screenshots/01_SETUP_ENVIRONMENT.png)


### 2. Terraform Initialization

Terraform backend and provider configuration were initialized.

![Terraform Init](screenshots/02_TERRAFORM_INIT.png)


### 3. Terraform Plan

Terraform plan was executed to preview infrastructure changes before deployment.

![Terraform Plan](screenshots/03_TERRAFORM_PLAN.png)


### 4. Terraform Apply

Infrastructure was deployed to AWS using Terraform.

![Terraform Apply](screenshots/04_TERRAFORM_APPLY.png)
