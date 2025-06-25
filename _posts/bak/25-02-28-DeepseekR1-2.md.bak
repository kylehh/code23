---
title: Deepseek R1 - GPT history
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Continue with Deepseek R1 from EZ Encoder 
[Link](https://www.youtube.com/watch?v=6fPvbIFF_wY)

## 0 Kimi K1.5
Flood Sung answered how K1.5 was trained on [Zhihu](https://www.zhihu.com/question/10114790245/answer/84028353434), explained how they learned from OAI o1 to get **Long Chain of Thoughts**. 

Both Noam and Richard emphasis on **search**, instead of any  **structured** methods like MCTS. Don't be limited by reward model in RL due to **reward hacking** limitatinos. So 

![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/shuati.png)

## 1 Inductive Bias
Both RNN and CNN has structured bias built in while Transformers doesn't have it, using only attention and MLP

## 2 The Bitter Lesson
Richard Sutton proposed use **general** method, which is easy to **scale up**, to improve **performance**
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/general.png)

## 3 History
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/tree.png)
Bert goes for traditional Encoder-Decoder pattern
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/bert.png)

GPT-1 paper, Improving Language Understanding by GPT. Alec made historical move of applying transformer's decoder.
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/gpt-1.png)
GPT-2 paper, Language Models are Unsupervised Multitask Learners. Use QA mode for all tasks, and see scaling law showing up. 
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/gpt-2.png) 
GPT-3 paper, Language Models are Few-Shot Learners, even zero shot can see improvement from scaling law.
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/gpt-3.png)
Google released their decoder only model, PaLM, with larger size. Similiar, Deepseek also incresae model sizes. Meta's OPT-175B has many failes. 
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/sizes.png)  
Instruct GPT paper, Training Language models to follow instructions with human feedback, critial RL during training: first SFT, then train a reward model, and RL through PPO.
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/instruct.png)

Reward hacking shows up when overoptimized. It's summarized in OAI paper, Leraning to summarize from human feedback. 
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/rewardhacking.png)

This is the overall paradim of LLM training from OAI
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/paradim.png)

## 4 DPO
DPO, Direct Preference Optimization, your language model is Secretly a Reward Model, was using by Llama 3, so can skip the reward model training, and out of RL schema. 
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/dpo.png)

## 5 Emergency
Google published Emergent Abilities of LLM in 2022
![Alt text](/assets/images/2025/25-02-28-DeepseekR1-2_files/emergency.png)