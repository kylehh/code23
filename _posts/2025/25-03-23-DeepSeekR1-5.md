---
title: Deepseek R1 - GRPO
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

EZ encoder's new [video](https://www.youtube.com/watch?v=JZZgBu8MV4Q&t) on [DeepSeekMath](https://arxiv.org/pdf/2402.03300)

## 1 Data collection
Collect 120B tokens, and train 1.3B model first before traning the 7B model. 
![Alt text](/assets/images/2025/25-03-23-DeepSeekR1-5_files/data.png)

## 2 PoT 
**Program-Of-Thought**, use program as thinking progress.   
**Tool-integrated Reasoning** is combining of CoT and PoT
![Alt text](/assets/images/2025/25-03-23-DeepSeekR1-5_files/pot.png)

## 3 RL and GRPO
The top down of RL. 
![Alt text](/assets/images/2025/25-03-23-DeepSeekR1-5_files/topdown.png)
**TRPO** is adding a constrain between old and new policy, let KL divergence limited.  
**PPO** is a simplied TRPO by removing the KL div but use a clipping method.  

**GRPO** is further simplifying PPO by removing value model and compute the average of rewards for multiple CoTs. And use each reward minus this average  as the advtange.
![Alt text](/assets/images/2025/25-03-23-DeepSeekR1-5_files/grpo.png)

The lower bound, clipping, and KL div are used to limit the model from changing too much
![Alt text](/assets/images/2025/25-03-23-DeepSeekR1-5_files/formula.png)