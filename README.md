# PYTERRA: AWS Web Servers with Terraform and an Application Load Balancer

Terraform configuration that provisions two EC2 web servers in separate Availability Zones, serves them through an Application Load Balancer, and installs Apache on each server using EC2 user data.

> If PYTERRA stands for something specific, add that meaning here.

## Architecture

```
            Internet
               |
   Application Load Balancer (HTTP listener)
               |
        Target Group (health checks)
          /                 \
   EC2 web server      EC2 web server
   (Availability Zone 1) (Availability Zone 2)
   Apache via user data  Apache via user data
```

## What Terraform Creates

- **Two EC2 instances** in different Availability Zones for redundancy
- **Security groups** that allow HTTP traffic to the load balancer and the web servers `<confirm rules>`
- **Application Load Balancer** with a listener on port 80
- **Target group** with health checks that route traffic only to healthy servers
- **EC2 user data** that installs and starts Apache on each instance

`<add the VPC and subnet details: default VPC, or a VPC created by this configuration>`

## Project Structure

```
.
├── main.tf              # <confirm file names>
├── variables.tf
├── outputs.tf
└── README.md
```

## Prerequisites

- An AWS account and an IAM user or role with permission to manage EC2, VPC, and ELB resources
- Terraform `<version>` installed
- AWS CLI configured with credentials (`aws configure`)

## Usage

```bash
terraform init        # download providers
terraform plan        # preview the changes
terraform apply       # create the infrastructure
terraform output      # show the load balancer DNS name
```

Open the load balancer DNS name in a browser. You should see the Apache welcome page, and refreshing the page can show responses from either server.

When you finish, remove the resources to avoid charges:

```bash
terraform destroy
```

## Checking Health

In the AWS console, open **EC2 > Target Groups**, select the target group, and check that both instances show **healthy**. If an instance is unhealthy, check its security group rules, the health check path, and whether Apache started correctly.

## Troubleshooting

- **Target shows unhealthy:** confirm the health check path and port, then check that Apache is running on the instance.
- **Load balancer times out:** confirm the load balancer's security group allows inbound HTTP and the web servers' security groups allow traffic from the load balancer.
- **No response from the DNS name:** check the listener rules and that the target group has registered targets.
