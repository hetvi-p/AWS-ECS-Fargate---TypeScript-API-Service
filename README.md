# AWS-ECS-Fargate---TypeScript-API-Service
This project sets up and deploys an API service using **AWS ECS Fargate**. The infrastructure is defined using **AWS CloudFormation**, and the service is containerized with **Docker** and stored in **AWS ECR**. The project also configures **Route 53** to associate a domain with the service and uses an **Application Load Balancer (ALB)** to manage traffic securely over **HTTPS**.

## Table of Contents
- [Project Overview](#project-overview)
- [Technologies Used](#technologies-used)
- [Skills Gained](#skills-gained)
- [Setup and Usage](#setup-and-usage)
- [Running Locally](#running-locally)
- [Deploying to AWS](#deploying-to-aws)
- [Contributing](#contributing)

## Project Overview
This project automates the creation of an API infrastructure on AWS using **ECS Fargate**, a serverless compute engine for containers. Initially, the infrastructure uses the default **NGINX** image, but you can customize it by creating your own Docker image, pushing it to AWS ECR, and updating the ECS service to use your image. The project includes:
- Creating the infrastructure with CloudFormation.
- Setting up **HTTPS** using a sub-domain.
- Pushing the app image to **AWS ECR**.
- Deploying the image using **AWS ECS Fargate**.

## Technologies Used
- **TypeScript**: Backend logic and API services.
- **AWS ECS Fargate**: Containerized application deployment.
- **Docker**: For containerizing the application.
- **AWS ECR**: Container image registry.
- **AWS CloudFormation**: Infrastructure as code to automate the creation and update of AWS resources.
- **AWS Route 53**: Domain name system (DNS) to link a sub-domain.
- **Application Load Balancer (ALB)**: To manage traffic and secure the application with SSL certificates.
- **AWS CLI**: For interacting with AWS services programmatically.

## Skills Gained
From this project, I gained experience in:
- **AWS ECS Fargate**: Setting up a scalable, serverless API service.
- **CloudFormation**: Automating infrastructure deployment using YAML templates.
- **Docker**: Containerizing applications and managing images.
- **AWS ECR**: Managing and deploying custom Docker images.
- **HTTPS & SSL Configuration**: Securing APIs with HTTPS and SSL certificates using an ALB.
- **Automation with AWS CLI**: Automating infrastructure management and deployments.
- **Networking with VPCs and Subnets**: Working with AWS networking to configure VPCs and subnets.

## Setup and Usage

### Prerequisites
Before you begin, ensure you have the following:
- An **AWS Account** with programmatic access.
- **AWS CLI** installed on your machine.
- A domain name registered in **AWS Route 53** (for example, `example.com`).

### Running Locally
To run the app locally for development:
1. Install dependencies:
   ```bash
   npm i
   ```
2. Run the development server:
   ```bash
   npm run dev
   ```
3. Open your browser and go to `http://localhost`.

### Deploying to AWS

To deploy the service to AWS, follow these steps:

#### 1. VPC and Subnet Setup
Ensure you have a **VPC** and two **subnets** created in the same AWS region.

#### 2. Create ECS Infrastructure
Run the following command to create the ECS Fargate infrastructure:
```bash
aws cloudformation create-stack --stack-name aws-ecs-typescript-api --template-body file://./ecs-1.yml --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=VPCID,ParameterValue=YOUR_VPC_ID ParameterKey=Subnet1ID,ParameterValue=YOUR_SUBNET_1_ID ParameterKey=Subnet2ID,ParameterValue=YOUR_SUBNET_2_ID ParameterKey=DomainName,ParameterValue=YOUR_DOMAIN_NAME.com
```

#### 3. Update Stack with HTTPS and Sub-domain
Once the stack is created, update it with the following command to enable HTTPS:
```bash
aws cloudformation update-stack --stack-name aws-ecs-typescript-api --template-body file://./ecs-2.yml --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=Subnet1ID,ParameterValue=YOUR_SUBNET_1_ID ParameterKey=Subnet2ID,ParameterValue=YOUR_SUBNET_2_ID ParameterKey=VPCID,ParameterValue=YOUR_VPC_ID ParameterKey=DomainName,ParameterValue=YOUR_DOMAIN_NAME.com
```

#### 4. Push Your Docker Image to AWS ECR
1. Build the Docker image:
   ```bash
   docker build -t aws-ecs-typescript-api .
   ```
2. Tag the image:
   ```bash
   docker tag aws-ecs-typescript-api:latest YOUR_AWS_ACCOUNT_NUMBER.dkr.ecr.YOUR_AWS_REGION.amazonaws.com/aws-ecs-typescript-api:latest
   ```
3. Push the image to AWS ECR:
   ```bash
   aws ecr get-login-password --region YOUR_AWS_REGION | docker login --username AWS --password-stdin YOUR_AWS_ACCOUNT_NUMBER.dkr.ecr.YOUR_AWS_REGION.amazonaws.com
   docker push YOUR_AWS_ACCOUNT_NUMBER.dkr.ecr.YOUR_AWS_REGION.amazonaws.com/aws-ecs-typescript-api:latest
   ```

#### 5. Update ECS to Use Your Docker Image
After pushing the image, update `ecs-2.yml` to reference your image in **AWS ECR**. Then, re-run the `update-stack` command:
```bash
aws cloudformation update-stack --stack-name aws-ecs-typescript-api --template-body file://./ecs-2.yml --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=Subnet1ID,ParameterValue=YOUR_SUBNET_1_ID ParameterKey=Subnet2ID,ParameterValue=YOUR_SUBNET_2_ID ParameterKey=VPCID,ParameterValue=YOUR_VPC_ID ParameterKey=DomainName,ParameterValue=YOUR_DOMAIN_NAME.com
```

## Contributing
If you'd like to contribute, feel free to fork the repository and submit a pull request. All contributions are welcome!
