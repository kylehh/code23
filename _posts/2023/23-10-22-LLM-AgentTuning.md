---
title: AgentTuning and AgentInstruct
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Came across another agent training paper from Tsinghua, [AgentTuning](https://thudm.github.io/AgentTuning/). 

The idea is similiar to FireAct, but it different training datasets to train the agent, and shows it has performance improvement on held-out tests, like HotpotQA. 

It also provides downloads of training datasets **AgentInstruct**.

I tried to finetune 7B and 13B with it, and record it in cookbook [here](https://github.com/anyscale/endpoint-cookbook/tree/main/App_AgentTune). Here are couple of findings:
1. The AgentInstruct datasets are NOT in GPT format, so the cookbook mainly convert it into the right format for FT.  
2. The agent perform has improvement but still not good enough to answer HotpotQA with LangChain ZeroShotAgent.
This is not surprising results, according to the paper, only the 70B reached the GPT3.5 level after FT.   
![Alt text](/code23/assets/images/2023/23-10-22-LLM-AgentTuning_files/agenttune.png)
3. The LoRA FTed model may have some weird output. According to engineer, it's normal output due to low quality of FT. I was bit surpised that how little data (~1800 entries) can change the behavior of a LLM using LoRA. 
![Alt text](/code23/assets/images/2023/23-10-22-LLM-AgentTuning_files/wrong.png)    
(There are actually more \<div> there)  
4. I also FTed with FireAct datasets and the performance is similiar, not good enough for agent, especially with math operations. and it's really easy to "overfit" even with ~400 data from FireAct. 

