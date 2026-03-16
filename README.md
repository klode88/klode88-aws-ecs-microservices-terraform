# AWS ECS Microservices with Terraform

## Project Overview

This project demonstrates how to deploy a **containerized microservices architecture on AWS** using **Terraform, Docker, and Amazon ECS Fargate**.

The application simulates a simplified **online marketplace platform** composed of two independent microservices:

* **Product Service** – handles product catalogue requests
* **Cart Service** – manages shopping cart operations

Both services run as Docker containers deployed on **AWS ECS Fargate** and are accessed through a single **Application Load Balancer (ALB)**.

All infrastructure is provisioned using **Terraform Infrastructure as Code (IaC)**.


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

# Architecture Decisions and Trade-offs

This project was designed to demonstrate a **production-style microservices architecture** while keeping operational complexity and cost under control. Below are the key architectural decisions and the reasoning behind each technology choice.

---

# Why ECS Instead of Kubernetes (EKS)

Amazon ECS was selected instead of Kubernetes (EKS) because the application architecture is relatively small and does not require the operational complexity of a full Kubernetes control plane.

Kubernetes provides extremely powerful orchestration capabilities such as custom schedulers, advanced networking, and large multi-cluster environments. However, it also introduces significant operational overhead including cluster management, control plane configuration, networking plugins, and continuous upgrades.

ECS integrates natively with AWS services such as IAM, CloudWatch, and ALB, making it much simpler to operate.

From a **cost perspective**, ECS also avoids the additional **EKS control plane cost (~$0.10/hour ≈ $73/month)**, which makes it more cost efficient for small or medium workloads.

For large organizations with hundreds of microservices or multi-cloud requirements, Kubernetes may be the better choice. For this project, ECS provides the same container orchestration capability with much lower operational complexity.

---

# Why Fargate Instead of EC2

AWS ECS supports two launch types:

* **EC2 launch type**
* **Fargate launch type**

Fargate was chosen because it is a **serverless container runtime**, meaning AWS manages the underlying compute infrastructure.

With EC2 launch type, engineers must manage:

* instance patching
* cluster scaling
* instance capacity
* security updates
* auto-scaling groups

Fargate eliminates all of that operational work.

From a **cost perspective**, EC2 can become cheaper at large scale because instances are shared across many containers. However, for small workloads or development environments, Fargate is often cheaper because you only pay for the CPU and memory your container actually uses.

Example pricing comparison:

Fargate example (small service):

* 0.25 vCPU
* 512MB RAM
* roughly **$0.012–$0.015 per hour**

EC2 example:

* t3.small instance ~ **$0.020 per hour**
* plus management overhead

Because this project only runs two small services, **Fargate provides better operational efficiency and predictable pricing**.

---

# Why Docker Containers Instead of Traditional Servers

Docker was used because containers provide a **portable and consistent runtime environment**.

Without containers, the application would need to run directly on virtual machines. This creates several problems:

* environment inconsistencies
* dependency conflicts
* harder deployments
* slower scaling

Docker packages the application and all dependencies into a single image. This image can run consistently across development, testing, and production environments.

Containers also allow faster scaling since new instances can start in seconds instead of minutes.

From a cost perspective, containers improve **resource utilization**, allowing multiple services to share compute resources efficiently.

---

# Why ECR Instead of Docker Hub

Amazon Elastic Container Registry (ECR) was used instead of public registries like Docker Hub.

The main reasons are **security, integration, and reliability**.

ECR integrates directly with:

* ECS
* IAM authentication
* AWS networking
* private repositories

This avoids exposing container images publicly.

From a **cost perspective**, ECR is very affordable:

* ~$0.10 per GB per month storage
* minimal network cost inside AWS

Docker Hub also enforces **pull rate limits**, which can cause deployment failures in production environments.

For production AWS environments, ECR is the recommended registry.

---

# Why Application Load Balancer Instead of Nginx or API Gateway

The Application Load Balancer was chosen because it provides **native layer-7 routing for microservices**.

It supports:

* path-based routing
* container target groups
* health checks
* automatic scaling
* direct integration with ECS

For example:

* `/` → Product Service
* `/cart` → Cart Service

Nginx could perform similar routing but would require managing EC2 instances.

API Gateway is better suited for **serverless architectures (Lambda)** rather than containerized services.

From a **cost perspective**, ALB typically costs:

* ~$0.0225 per hour
* plus request processing

For small workloads this is roughly **$15–$20 per month**, which is acceptable for production-grade routing.

---

# Why a VPC with Public and Private Subnets

The architecture separates infrastructure into two layers:

Public Layer

* Application Load Balancer
* Internet Gateway

Private Layer

* ECS services
* containers

This design improves **security and network isolation**.

Containers are not directly exposed to the internet. Instead, all traffic passes through the ALB.

This is a common best practice in production AWS architectures.

---

# Why a NAT Gateway Was Required

Containers run inside **private subnets**, which means they cannot access the internet directly.

However, they still need outbound access for tasks such as:

* pulling Docker images from ECR
* accessing external APIs
* downloading dependencies

The NAT Gateway allows private resources to access the internet while preventing inbound connections.

The NAT Gateway requires an **Elastic IP** so that outbound traffic has a consistent public IP address.

From a cost perspective:

NAT Gateway pricing is approximately:

* **$0.045 per hour (~$32/month)**
* plus data processing charges

For development environments, this cost can be reduced by destroying infrastructure when not in use.

---

# Why Microservices Instead of a Monolithic Application

This system uses two independent services:

* Product Service
* Cart Service

This microservice approach allows:

* independent scaling
* isolated deployments
* fault isolation
* easier feature expansion

For example, if the Cart Service experiences heavy load, it can scale independently without affecting the Product Service.

The trade-off is increased infrastructure complexity compared to a single monolithic application.

However, modern cloud platforms and container orchestration tools make microservices much easier to manage.

---

# Cost Optimization Considerations

Several decisions were made to reduce infrastructure cost while maintaining production architecture patterns:

* Fargate tasks use **small CPU/memory configurations**
* Only **one container per service** runs
* ECS services scale horizontally only when required
* NAT Gateway can be destroyed after testing
* CloudWatch log retention limited to **7 days**


              

