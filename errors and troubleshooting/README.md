## Errors Encountered During the Project

- **01 IAM Permission Error**  
Terraform initially failed to create AWS resources because the IAM user did not have sufficient permissions. Additional IAM permissions were granted to allow infrastructure deployment.

- **02 Terraform Not Initialized**  
Terraform commands failed before running `terraform init`. Initializing the project downloaded the required AWS provider plugins.

- **03 Incorrect Resource Dependencies**  
Some resources attempted to deploy before others were created. Terraform dependencies were corrected using proper resource references.

- **04 NAT Gateway Routing Issue**  
Private subnet resources could not access the internet because the route table was not pointing to the NAT Gateway. The private route table was updated to use the NAT Gateway.

- **05 ALB Listener Priority Conflict**  
Terraform failed when creating an ALB listener rule because the same priority value was already used. The listener rule priority was updated to a unique value.

- **06 Duplicate Terraform Resource Definitions**  
Terraform validation failed due to duplicate resource declarations in the configuration file. The duplicate resource blocks were removed.

- **07 Docker Image Not Available in ECR**  
ECS tasks failed to start because the Docker image had not been pushed to Amazon ECR. The image was built, tagged, and pushed to ECR.

- **08 Incorrect Container Port Mapping**  
The ECS service failed health checks because the container port did not match the target group port. The ports were aligned to use port 80.

- **09 Load Balancer Routing Misconfiguration**  
Requests to `/cart` were not reaching the cart service due to an incorrect path pattern. The listener rule was corrected to properly route `/cart*` requests.

- **10 ECS Security Group Restriction**  
The ECS service could not receive traffic from the load balancer because the security group rules were incorrect. Inbound traffic from the ALB security group was allowed.
