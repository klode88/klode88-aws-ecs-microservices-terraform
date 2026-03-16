rvices with Terraform
Project Overview

This project demonstrates how to deploy a containerized microservices architecture on AWS using Terraform, Docker, and Amazon ECS Fargate.

The application simulates a simplified online marketplace platform composed of two independent microservices:

Product Service – handles product catalogue requests

Cart Service – manages shopping cart operations

Both services run as Docker containers deployed on AWS ECS Fargate and are accessed through a single Application Load Balancer (ALB).

All infrastructure is provisioned using Terraform Infrastructure as Code (IaC).

Architecture Diagram
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
Architecture Overview

The application runs inside a custom Amazon VPC using a secure multi-tier architecture.

Core AWS Services Used

Amazon VPC

Public Subnets

Private Subnets

Internet Gateway

NAT Gateway with Elastic IP

Application Load Balancer (ALB)

Amazon ECS (Fargate)

Amazon ECR (Container Registry)

Amazon CloudWatch Logs

AWS Cloud Map (Service Discovery)

Terraform Infrastructure as Code

Microservices
Product Service

The Product Service handles product catalogue requests.

Example endpoint:

http://ALB-DNS/

Response:

Product Service Running
Cart Service

The Cart Service handles shopping cart functionality.

Example endpoint:

http://ALB-DNS/cart

Response:

Cart Service Running
System Traffic Flow

The system processes user requests using the following flow:

A user sends a request to the Application Load Balancer (ALB).

The ALB evaluates listener routing rules.

Requests to / are routed to the Product Service.

Requests to /cart are routed to the Cart Service.

The ALB forwards traffic to the appropriate Target Group.

The Target Group sends traffic to the ECS Service running on Fargate.

ECS launches containers from images stored in Amazon ECR.

The container processes the request and sends the response back through the ALB.

Networking Architecture

The infrastructure uses a public/private subnet architecture.

Public Layer

Application Load Balancer

Internet Gateway

Private Layer

ECS Fargate containers

Application services

Containers run inside private subnets and are not directly accessible from the internet.

Outbound internet access is handled through a NAT Gateway.

Why the NAT Gateway Uses an Elastic IP

The NAT Gateway requires an Elastic IP because it must provide a stable public IP address for outbound internet traffic.

This allows private ECS containers to:

Pull Docker images from Amazon ECR

Access external APIs

Install updates

Without exposing the containers directly to the internet.

This architecture improves security, scalability, and network isolation.

Infrastructure Deployment

All infrastructure in this project is provisioned using Terraform.

Terraform creates:

VPC network

Public and private subnets

Internet Gateway

NAT Gateway with Elastic IP

Security groups

Application Load Balancer

Target groups

ECS cluster

ECS services

ECR repositories

CloudWatch log groups

Service discovery

