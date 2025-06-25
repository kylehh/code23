---
title: 4Bit Quantization GPTQ and GGUF and 1Bit LLM
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
 

