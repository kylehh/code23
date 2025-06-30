---
title: ECS Deployment Details
mathjax: true
toc: true
categories:
  - Application
tags:
  - AWS
---

Add Dynamo example into ECS, couple of pitfalls

## 1 Cluster Setups
1. GPU support only comes with EC2 cluster, so NO Fargate
2. AMI has to be GPU compatable (Amazon Linux 2 GPU)
  - [Bottlerocket](https://github.com/bottlerocket-os/bottlerocket) is an open-source Linux-based operating system meant for hosting containers. (somehow I can't ssh into it)
3. Setup **Security Group** properly for inbound traffic
4. Add SSH key 
5. Set proper EBS volume size

## 2 Task defination Setups
1. Choose EC2 so we can modify the **network mode**
  - **awsvpc** network mode is required for tasks hosted on Fargate.
  - Set **bridge** mode so the container can get outbound traffic
  - Still not sure why awsvpc mode failed to send outbound traffic
2. Task size, allocate resource for the task, and cause ASG(Auto Scaling Group to work)
3. Container resource limit, set GPU=1 to get gpu nodes
4. Add `ETCD_ENDPOINTS` and `NATS_SERVER` env var.
5. Container configurations, set entrypoint as `sh -c` and add actually dynamo command as command.

## 3 Tips and Trouble shootings
1. Multi-node deloyment can be achieved by different tasks. Like one task for etcd/nats.
2. Multiple containers in one task can cause non-repeatable errors, so split the containers into multiple tasks

## 4 Dynamo Deployment of Hello World Example on AWS ECS
### 1. ECS Cluster Setup 
1. Go to AWS ECS console, **Clusters** tab and click on **Create cluster**
2. Input the cluster name and choose **AWS Fargate** as the infrastructure. This option will create a serverless cluster to deploy containers
3. Click on **Create** and a cluster will be deployed through cloudformation.
### 2. Task Definations Setup
We need to start 3 containers for the hello world example. A sample task defination JSON is attached.
1. ETCD container
- Container name use `etcd`
- Image URL is `bitnami/etcd:3.6.1` and **Yes** for Essential container
- Container port  

|Container port|Protocol|Port name| App protocol|
|-|-|-|-|
|2379|TCP|2379|HTTP|
|2380|TCP|2379|HTTP|
- Environment variable key is `ALLOW_NONE_AUTHENTICATION` and value is `YES`
2. NATS container
- Container name use `nats`
- Image URL is `nats:2.11.4` and **Yes** for Essential container
- Container port  

|Container port|Protocol|Port name| App protocol|
|-|-|-|-|
|4222|TCP|4222|HTTP|
|6222|TCP|6222|HTTP|
|8222|TCP|8222|HTTP|
- Docker configuration, add `-js, --trace` in **Command**

3. Dynamo hello world pipeline container
- Container name use `dynamo-hello-world-pipeline`
- Add your Image URL and **Yes** for Essential container. It can be AWS ECR URL or Nvidia NGC URL. If using NGC URL, please also choose **Private registry authentication** and add your Secreate Manager ARN or name. 
- Container port  

|Container port|Protocol|Port name| App protocol|
|-|-|-|-|
|8000|TCP|8000|HTTP|

- Environment variables

|Key|Value type|Value|
|-|-|-|
|ETCD_ENDPOINTS|Value|http://localhost:2379|
|NATS_SERVER|Value|http://localhost:4222|
- Docker configuration  
Add `sh,-c` in **Entry point** and `cd src && uv run dynamo serve hello_world:Frontend` in **Command**

### 3. Task Deployment
You can create a service or directly run the task from the task defination
1. Environment setup
- Choose the Fargate cluster for **Existing cluster** created in step 1.
2. Networking setup
- Make sure you security group has inbound rule for port 22, so that you can ssh into the instance for debugging purpose
- Turn on **public IP**

### 4. Testing


