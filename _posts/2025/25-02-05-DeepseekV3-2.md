---
title: Deepseek V3 - MoE
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

The Deepseek MoE was introduced in this [paper](https://arxiv.org/pdf/2401.06066)

## 1. Mix of Experts
In tradition transformer architecture:
1. the attention layer has most computation cost
2. The FFN layer has most of the parameters (knowledge storage)
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/overview.png)
MoE is to split FFN into multiple experts. Dense MoE would do weighted sum for **all experts**, while sparse MoE is **top K experts**.
The gate here are actually FFN or MLP network.
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/moe.png)

## 2. Deepseek MoE
Two improvements from DeepseekMoE
1. Increase number of experts (k=2->4, x2) and reduce each experts size (MLP size by half). So the total parameter size is the same.
It's similar to model ensamble in Kaggle compeitation, use quantity to trade off quality.
2. Add a shared expert. It can help model correction.
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/dsmoe.png)

## 3 Switch Transformer
Load balancing is key issue in MoE training. To make sure each expert get similar amount of tokens for training, we can do **loss control**, adding a loss term to balance the load based on [switch transformer](https://arxiv.org/pdf/2101.03961)

This paper proposed to calculate two vectors
1. The probability of each expert got chosen during training. It's the **actally distribution** of experts got trained
2. The average probability of each expert chosen by the router. It's the **theoretical distribution** of experts.
The load balance loss is the differences between these two vectors. 
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/loss.png)
By finding the **inner product** of these two vectors, authors though it encourage uniform distribution, which leads to minimun loss. 
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/innerp.png)
But this is WRONG. Intuitively, the smaller the inner product, the larger the differences of the two vectors. So the it's easy to find a counter-example of non-uniform distribution has smaller loss values
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/wrong.png)

## 4 Auxilary-Loss-Free Load Balancing
DeepseekMoE use loss less control by introduing a bias in the softmax layer in this Auxilary-Loss-Free Load Balancing [paper](https://arxiv.org/pdf/2408.15664)
The performance, measured by MaxVio, is better than Loss controlled LB.
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/maxvio.png)

Couple of other points in DeepseekMoE
1. Node limit routing: Limit token sent to N nodes
2. No Token dropping in both training and inference: Token dropping is use the skip connection ONLY. 
![Alt text](/code23/assets/images/2025/25-02-05-DeepseekV3-2_files/others.png)