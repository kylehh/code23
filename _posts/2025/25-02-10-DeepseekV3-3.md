---
title: Deepseek V3 - MTP
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Great explanation of the MTP used in Deepseek V3. Video [source]() is part 4 of this series.
## 1 Overview
Multi-Token-Prediction of Deepseek is applying Eagle's causal heads idea, into Meta's MTP training([paper](https://arxiv.org/pdf/2404.19737)).  
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/overview.png)

## 2 MTP Training 
Using multiple heads to predict mulitple tokens during training.
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/metamtp.png)
and it can have following advantages
1. Enhaunce training signal
2. Planning capability. Not just one short-sighted token only, but can predict future tokens
3. Solve the Teaching Forcing issue. In the following example, 3->5 is a hard transition. Next token prediction can only get 1/7 training on the hard transition, but the MTP can get 6/21 training on it.
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/solvetf.png)
Here is another explanation of Teaching Force in training. You can see the model prediction 乐 instead of 迎 after 欢. But in the next round of training, the input was forced backed to 迎. 
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/tf.png)
MTP works on relative large size models, but it's not working on smaller models. (pass@k meaning pass k unit tests). Still MTP can improve training efficiency and improve reasoning capability.
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/mtp.png)

## 3 Speculative Decoding
An analagy of Speculative Decoding with two chiefs. 
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/speculative.png)
Another example is **branch prediction**. A CPU would predict which branch of code would execute so it calculate it ahead of time. 
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/branch.png)
There are two major branches of SD, one is independanc path, having two models, one big and one small, as in the orignal Google/Deepmind paper.
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/ind.png)
Medusa absorb the small model into the big model, by predicting multiple tokens by **parellel heads**, and eagle improve it by changing to **causal heads**
This picture if from KOALA [paper](https://arxiv.org/pdf/2408.08146), which improve both method by adding K-layers.
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/koala.png)

## 4 MTP in Deepseek
MTP with causal heads, that's the MTP used in deepseek
![Alt text](/assets/images/2025/25-02-10-DeepseekV3-3_files/deepseek.png)