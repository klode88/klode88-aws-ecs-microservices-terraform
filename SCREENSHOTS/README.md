## Infrastructure Deployment

### Environment Setup
Terraform and the AWS CLI were configured locally to prepare the environment for infrastructure deployment. The project workspace was created in Visual Studio Code and connected to the AWS account.



### Terraform Initialization
Terraform was initialized to download the required AWS provider plugins and prepare the project backend. This step ensures Terraform can communicate with AWS and manage resources.



### Terraform Plan
Terraform generated an execution plan showing all AWS resources that would be created. This step allows verification of the infrastructure before deployment.

### Terraform Apply
Terraform applied the configuration and automatically created the infrastructure resources in AWS, including networking, security groups, load balancing, and container services.


### Virtual Private Cloud (VPC)
A dedicated VPC was created to isolate the application infrastructure. This provides full control over networking, routing, and security within the environment.


### Public and Private Subnets
Public and private subnets were created across multiple Availability Zones. Public subnets host internet-facing components such as the load balancer, while private subnets host the application services securely.


### NAT Gateway
A NAT Gateway was deployed in the public subnet. This allows services running inside private subnets to access the internet for tasks such as pulling Docker images from Amazon ECR while remaining inaccessible from the public internet.


### Application Load Balancer
An Application Load Balancer (ALB) was created to serve as the public entry point for the system. The load balancer distributes incoming traffic to the correct backend services.

### Listener and Target Groups
The ALB listener was configured with target groups to route traffic to the appropriate services. Path-based routing allows different URLs to reach different microservices.



### ECS Cluster
An Amazon ECS cluster was created to run containerized services using AWS Fargate. This removes the need to manage EC2 instances while still providing scalable container orchestration.


### Elastic Container Registry (ECR)
Amazon ECR repositories were created to store Docker images for the microservices. These images are pulled by ECS when starting the containers.


### Product Service Running
The product service container was successfully deployed in ECS and registered with the load balancer. Requests reaching the root path are forwarded to this service.


### Cart Service Running
The cart service container was deployed as a separate microservice. Path-based routing through the load balancer directs requests containing `/cart` to this service.
