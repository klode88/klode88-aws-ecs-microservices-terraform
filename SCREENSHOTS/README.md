## Infrastructure Deployment (Step-by-Step)

### 01 Environment Setup
Terraform and the AWS CLI were configured locally to prepare the environment for infrastructure deployment. The project workspace was created in Visual Studio Code and connected to the AWS account.


### 02 Terraform Initialization
Terraform was initialized to download the required AWS provider plugins and prepare the project backend. This step allows Terraform to communicate with AWS and manage infrastructure resources.


### 03 Terraform Apply
Terraform apply was executed to start creating the infrastructure resources automatically in AWS according to the Terraform configuration.


### 04 Terraform Plan
Terraform plan was used to preview the infrastructure changes before deployment. This step verifies what resources will be created, modified, or destroyed.


### 05 IAM Permission Error
During the initial deployment attempt, an IAM permission error occurred. This highlighted missing permissions required for Terraform to create AWS infrastructure resources.


### 06 IAM Permissions Added
Additional IAM permissions were added to the user to allow Terraform to successfully create the required AWS infrastructure resources.


### 07 VPC Created
A dedicated Virtual Private Cloud (VPC) was created to isolate the application infrastructure and provide full control over networking, routing, and security.


### 08 Terraform Documentation Reference
Terraform documentation was used to verify the correct configuration for networking components such as the Internet Gateway and routing rules.


### 09 Internet Gateway Created
An Internet Gateway was attached to the VPC to allow internet connectivity for resources located in the public subnets.


### 10 Public and Private Subnets
Public and private subnets were created to separate internet-facing resources from internal application services.


### 11 Subnets Across Availability Zones
Subnets were distributed across multiple Availability Zones to improve availability and resilience of the infrastructure.


### 12 Public Route Table
A public route table was configured to direct internet-bound traffic from the public subnets through the Internet Gateway.


### 13 NAT Gateway Created
A NAT Gateway was deployed to allow services running in private subnets to access the internet securely for tasks such as pulling Docker images.


### 14 Application Load Balancer
An Application Load Balancer (ALB) was created to serve as the public entry point for the system and distribute incoming traffic across the backend services.


### 15 ALB Listener and Target Groups
The ALB listener and target groups were configured to route incoming HTTP traffic to the appropriate ECS services using path-based routing.


### 16 ECS Cluster
An Amazon ECS cluster was created to run containerized microservices using AWS Fargate, removing the need to manage EC2 instances.


### 17 ECR Repositories and Docker Images
Amazon Elastic Container Registry (ECR) repositories were created to store Docker images for the application services before deployment.


### 18 Product Service Running
The product service container was successfully deployed and running within the ECS cluster, accessible through the load balancer.


### 19 Cart Service Running
The cart service container was deployed as a separate microservice, with path-based routing directing `/cart` requests to the correct backend service.


