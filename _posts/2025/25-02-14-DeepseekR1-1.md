---
title: Deepseek R1 - RL review
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Taking notes from EZ Encoder Academy's video [series](https://www.youtube.com/watch?v=_dLlfAPuilM) about R1. 

## 1 What's is AGI 
Part 1 of the video is explain difference of R1 Zero, using RL only for post-training, and R1, using bootstrapping with cold start. I will skip most of it, and one interesting topic is about security is deepseek, which is 100% vunlerable against attackss
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/hack.png)

A google [paper](https://arxiv.org/pdf/2311.02462) gives the defination of different levels of AGI
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/agi.png)

Deepmind, OAI uses RL to achieve RL, and OAI is more focus on LLM approach since ChatGPT. LLM can be considered as RL, as context is **environment**, LLM is the **agent**, and next token is the **action**. LeCun is thinking JEPA(Joint Embedding Predictive Architecture) is the way to achieve world model. See details [here](https://www.turingpost.com/p/jepa)
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/dol.png)

## 2 RL in Review
Comparing Supervised Learning with RL, the GT for SL is very dense, have GT for each data point, while GT for SL is **sparse**, providing rewards after several steps.
A [paper](https://arxiv.org/pdf/2501.17161) summarized it as "SFT Memorizes, RL Generalizes"
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/sftrl.png)

**Policy based method** is to train the agent to take action, like agent being player(actor). **Value based method** is to get a value for agent actions, like a coach to the agent(critic). These two can be combined as in **Actor-Critic method**
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/actorcritic.png)

## 3 How RL is different from SL
1. Reward is sparse, so most of the training and optimization tricks in RL is to **get sparse reward properly handel to training the policy network. Like both PPO and GRPO.**

2. Reward defination can be in **verifiable domain**, like coding/math/game, or **unverifiable domain**, based on human perference. This could leads to **reward hacking**, like Terminators want to terminate human, which is easy way to achieve best rewards for protecting the earth. 

3. RL dynamically interacted with the environment, so we can simulate the states between two RL agents, like playing go games.

4. Explore vs Exploit. 

## 4 AlphaGo and AlphaZero
Essencially, it's just search algorithm behind the scene, with RL used to help trim search pathes
1. For simple game like tic-tac-toe, $10^4$, brutal force search can solve the problem, which is **Monte-Carlo simulation**.   
2. For Chess, $10^{47}$, DeepBlue uses **rule-based** search algo.
3. For Go, $10^{170}$, AlphaGo uses **Policy and Value network** to help the search, thus RL based searching. 

The training of AlphaGo has three steps
1. SL with human datasets
2. Train the policy network with self-play to learn how to play
3. Train the value network from self-play data to evaluate states
So Move 37 from AlphaGo was considered bad move, but eventually is a great move
Move 78 from Lee was un-common move, which helped Lee to beat AlphaGo.
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/alphago.png)

After removing the SL from step 1, DeepMind trained AlphaZero.
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/alphazero.png)

## 5 Book Recommend
What life should mean to you. On my to-do list now
![Alt text](/assets/images/2025/25-02-14-DeepseekR1-1_files/book.png)
