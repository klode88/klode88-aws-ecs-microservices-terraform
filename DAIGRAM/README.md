
Network Architecture Explanation

The application runs inside a Virtual Private Cloud (VPC), which is a private network in AWS where all the infrastructure is deployed. The VPC allows full control over how resources communicate with each other and how they access the internet.

Inside the VPC, the network is divided into two types of subnets: public subnets and private subnets.

Public subnets are used for resources that must be reachable from the internet. In this project, the Application Load Balancer (ALB) is placed in the public subnets. This allows users on the internet to send requests to the application.

Private subnets are used for resources that should not be directly exposed to the internet. The ECS containers running the Product Service and Cart Service are placed inside these private subnets. This design improves security because the containers cannot be accessed directly from outside the network.

When a user sends a request to the system, the request first reaches the Application Load Balancer. The load balancer acts as the entry point and decides where the request should go. It uses routing rules to forward traffic to the correct service. Requests to the root path go to the Product Service, while requests to /cart are routed to the Cart Service.

The load balancer forwards traffic to the ECS services using target groups. These target groups contain the running containers that process the requests and return the response to the user.

Although the containers are in private subnets, they still need outbound internet access for certain operations. For example, they may need to pull Docker images from Amazon Elastic Container Registry (ECR) or communicate with external APIs.

To allow this, the private subnets send outbound traffic through a NAT Gateway. The NAT Gateway is placed in a public subnet and acts as a bridge between the private network and the internet. This allows containers to access external services while still preventing inbound connections from the internet.

The NAT Gateway requires an Elastic IP address, which gives it a fixed public IP. This ensures that outbound traffic from the private containers always appears to come from the same IP address.

This network design provides several advantages. It protects internal services from direct internet exposure, centralizes traffic through the load balancer, and allows the application to scale securely inside the private network.
