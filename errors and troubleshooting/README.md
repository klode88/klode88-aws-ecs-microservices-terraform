## Errors Encountered During the Project

- **01 IAM Permission Error**  
Terraform initially failed to create AWS resources because the IAM user did not have sufficient permissions. An IAM group with the required policies was created and the user was added to the group to follow best practices.

- **02 Terraform Not Initialized**  
Terraform commands failed before running `terraform init`. Initializing the project downloaded the required AWS provider plugins and prepared the working directory.

- **03 NAT Gateway Routing Issue**  
Services inside private subnets could not access the internet because the route table was not configured correctly. The private route table was updated to send internet traffic through the NAT Gateway.

- **04 ALB Listener Priority Conflict**  
Terraform failed when creating an ALB listener rule because the priority value was already in use. The listener rule priority was updated to a unique value.

- **05 Duplicate Terraform Resource Definitions**  
Terraform validation failed because the same resource was declared more than once in the configuration. The duplicate resource block was removed.

- **06 Docker Image Not Available in ECR**  
ECS tasks initially failed to start because the Docker image had not yet been pushed to Amazon ECR. The image was built, tagged, and pushed to the repository.

- **07 Incorrect Container Port Mapping**  
The ECS service failed health checks because the container port did not match the target group configuration. Both were aligned to use port 80.

- **08 Load Balancer Routing Misconfiguration**  
Requests to `/cart` were not routed correctly due to an incorrect path pattern in the ALB listener rule. The routing rule was updated to match `/cart*`.

- **09 ECS Security Group Restriction**  
The ECS tasks were not reachable because the security group rules did not allow traffic from the Application Load Balancer. The ECS security group was updated to allow inbound traffic from the ALB security group.
