---
title: Building Slack Bot for LLM on AWS - Part 1
mathjax: true
toc: true
categories:
  - Application
tags:
  - AWS
  - LLM
  - GenAI
---

Kind of a hackathon/weekend project. Built a Slack bot running backend on Anyscale.
Actually my very first engineer project did at Amazon was using slack bot as frontend.
It was SA-launch and final project was to create a whatever service using AWS service. I was in charge of backend, and another guy used Slack bot created a frontend which can taking in an photo of a food ingredient tabel and later gives a warning if certain allergetic ingredients are found in the list. I was quite amazed by how easy it was to setup the whole UI pipeline using Slack bot, and the infrastructure of the backend, which is API Gateway plus Lambda function, was reused again and again in my later prototypes. 

I surely already forgot how to set up a Slack bot, instead of Googling online, I turned to ChatGPT for suggestions. One simple prompt and all the detailed steps are listed below, very detailed without any mistakes. 

1. API Gateway: Standard setup, trigger a Lambda
2. Lambda: Get event from API Gateway, extract info and sent to SQS. Another Lambda to process the event, which is triggered by SQS. The main logic is to create an Anyscale job to run the main processing. In this example, it was using Stable Diffusion models to generate images based on user's input prompt
3. SQS: Standard setup
4. Secrete Manager: It's used to store API tokens from Huggingface, OpenAI, etc


