# Automate ECS Fargate instrumentation using Terraform 

Amazon Elastic Container Service (ECS) is a scalable container management service that makes it easy to run, stop, and manage Docker containers on Amazon EC2 clusters.

This project demonstrates how AppDynamics agents can be embedded into an existing ECS/Fargate Terraform using Terraform. 

To ensure that the customer's application image is kept intact, and that the deployment process is immutable,  we have leveraged AWS CloudFormation's Depends-On feature to dynamically acquire the AppDynamics agent image from a Dockerhub, copy the content of the agent image into an emphemeral volume, then configure mount the shared volume into the main application's container at runtime. 


# Default Resources - without AppDynamics 
This demo creates the following AWS resources:

- VPC
- One public and one private subnet per AZ
- Routing tables for the subnets
- Internet Gateway for public subnets
- NAT gateways with attached Elastic IPs for the private subnet
- Security groups - that allows access to the specified container port
- An Application Load Balancer (ALB) -  with listeners for port 80
- An ECS cluster with a service - inculding auto scaling policies for CPU and memory usage, 
   -  Task definition to run docker containers
   -  IAM execution role
- Secrets - Creates secrets in Secret Manager

![aws](https://user-images.githubusercontent.com/2548160/111489223-da447980-8731-11eb-8dc7-260ab6c63121.png)
(Source: https://aws.amazon.com/de/blogs/compute/task-networking-in-aws-fargate/)

# AppDynamics 

- Modify the CloudFormation template to add `DependsOn` and AppDynamics environment variables   - https://github.com/Appdynamics/appdynamics-terraform-ecs-fargate/blob/main/template/app.json.tpl
- Create AppDyamics secret. Remove .example from the file name  - https://github.com/Appdynamics/appdynamics-terraform-ecs-fargate/blob/main/secrets.auto.tfvars.example
- Populate <a href="https://github.com/Appdynamics/appdynamics-terraform-ecs-fargate/blob/main/appdynamics.auto.tfvars">`appdynamics.auto.tfvars`</a> with your controller credentials and the agent's container registry. 


# Run 

Before we can start talking to an AWS account, we have to setup the Terraform provider, which looks something like this: 

```yaml
provider "aws" {
  access_key = var.aws-access-key
  secret_key = var.aws-secret-key
  region     = "eu-central-1"
  version    = "~> 2.0"
}
```

You can actually also leave out access_key and secret_key, then Terraform will use the values stored in your .aws/config.

# Sets up Terraform to run
`$ terraform init`

# Executes the Terraform run
`$ terraform apply`

# Now, move into the dev environment
`$ cd ../env/dev`

# Sets up Terraform to run
`$ terraform init`

# Executes the Terraform run
`$ terraform apply`

