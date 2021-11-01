---
title: "Aws-Getting started"
categories:
  - aws
tags:
  - EC2
  - ECS
  - ECR
---

- Download and install AWS CLI

```sh
 aws --version
 aws configure
```

- Login to your aws account EC2 (Amazon Elastic Compute Cloud EC2 console management)
  <https://console.aws.amazon.com>
  <https://eu-west-3.console.aws.amazon.com/>

- Create an IAM User and attach policies
  <https://console.aws.amazon.com/iamv2>

IAM: IAM stands for Identity and Access Management.
IAM Roles: A role is a set of permissions for an AWS service.
Policies: A policy is a collection of permissions for a specified services.

Create User->Attach existing policies->AmazonECS_FullAccess

- Create an ECR policy: ECR_FullAccess
  Choose service Elastic Container Registry.
  Then, attach the new policies to the IAM user.

- Create a repository an ECR registry.
  <https://eu-west-3.console.aws.amazon.com/ecr>

copy the repository uri, then tag your image with that uri:
docker tag myapp 277912921119.dkr.ecr.eu-west-3.amazonaws.com/ascode

In order for ECR to know which repository we are pushing our image to we must tag the image with that URI.

Next, we need to generate a ECR login token for docker. Docker needs that token to push to your repository.
aws ecr get-login-password --region eu-west-3 | docker login --username AWS --password-stdin 277912921119.dkr.ecr.eu-west-3.amazonaws.com

- Upload docker image to ECR
  docker build "C:\Users\staieb\Desktop\AsCode"
  -f "C:\Users\staieb\Desktop\AsCode\AsCode.Sts\Dockerfile"
  -t ascode-sts
  -t 277912921119.dkr.ecr.eu-west-3.amazonaws.com/ascode  
  --no-cache
  --force-rm

docker push 277912921119.dkr.ecr.eu-west-3.amazonaws.com/ascode

- Create a task in ECS
  <https://eu-west-3.console.aws.amazon.com/ecs>
  Task Definitions->Create->EC2
  Network mode: default
  Task memory: 512
  Task CPU : 128
  Add container (copy/paste ecr image uri)
  Port mapping: 0->443 (dynamic port assigning)

- Create security groups:
  <https://https://eu-west-3.console.aws.amazon.com/ec2>
  Security groups->create a group "my-alb"->Add ibound rule: Https from any where
  Security groups->create a group "my-ecs"->Add ibound rule:
  Custom TCP :from 32768-65535 sg-xxxxxxxx-of-my-alb

-Create a cluster in Elastic Container Service (ECS)
<https://eu-west-3.console.aws.amazon.com/ecs>

Clusters->EC2 Linux+Networking
EC2 instance type: t2.micro
Number of instances: 3
Security group: my-ecs

- Create a Load balancer.
  <https://https://eu-west-3.console.aws.amazon.com/ec2>

Type: Application load balancer
set listeners
create target group -> type instances->vpc

- Create a cluster service
  <https://eu-west-3.console.aws.amazon.com/ecs>

Cluster->Services->Create
Launch type: EC2
Load balancer type: Application Load balancer
path pattern: /\*, Evaluation order: 1

- What is a VPC
  A VPC is a virtual private cloud, it's a logicallu isolated portion of the aws cloud within a region.
  A subnet is a segment of VPC's Ip address range

What is aws cloudformation?
It's an infrastructure as code provider that allows you to define the stack with all resources.

What is Container Orchestration Tool ?

It's a tool for managing, scaling and deploying containers:

- Kubernates
- Docker Swarm
- Ecs: Elastic Container Service (ECS)

How does ECS work ?

ECS is a managed service to run containers

- Run containerized application cluster on AWS.
- ECS cluster contains services to manage container.
  - Where are these containers hosted

Steps:

- Build docker into an image
- uplod docker image to ECR
- Define aa ECS task: tells ecs how to to spin up your docker ocontainers
  - inside a task: deine containers, ports mapping, configure communication ports between those containers
    (microservices)
- Define a cluster: a task runner
- Define a service: like load balancer

What if you want to delegate infrastructure management also to aws
(container orchestration, hosting infrastrucrure management)

It's by using Aws Fargate:

- It's an alternative to EC2 instances
  -It's a serverless way to luch containers (No ECS server instance in AWS account)

- <https://console.aws.amazon.com/>
  (On the left-side of the bottom navigation bar, choose the language menu

- Sign in to the AWS Management Console
  <https://console.aws.amazon.com/iam/>

- In the navigation Pane, choose Users.
  Create Access key
  User->Security credentials-> Create access key

-Configure aws cli:

```sh
#Access key, Secret access key, region (Change default region: eu-west-3)
aws configure
```

- Docker File

- Elastic Container Registry (ECR)

- Elastic Container Service (ECS)
  - Cluster: logical grouping of hardware resources
  - Task: template that contains metadata aboput how how you should deploy your docker container (memory, cpu, port mappings, env...)
  - Service: advanced cased for deploying, scaling...

Create cluster

```sh

```

## Links

<https://www.youtube.com/watch?v=CRp354oWUJA>

<https://towardsdatascience.com/deploying-a-docker-container-with-ecs-and-fargate-7b0cbc9cd608>

<https://www.youtube.com/watch?v=nhqcecpi47s>
