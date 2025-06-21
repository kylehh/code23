---
title: Deepseek V3 - MLA
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Let's summarize the learning of [deepseek V3](https://arxiv.org/abs/2412.19437) from recent weeks

## 0 Testing datasets
- [MMLU-Pro](https://github.com/TIGER-AI-Lab/MMLU-Pro): Univ of Waterloo's Multi-Task Language Understanding Benchmark, enhaunced and released in 2024.12,000 Qs
- [GPQA](https://huggingface.co/datasets/Idavidrein/gpqa)Graduate-Level Google-Proof Q&A Benchmark. 448 Qs
- [Math-500](https://huggingface.co/datasets/HuggingFaceH4/MATH-500) OpenAI's Math test sets.
- [CodeForces](https://codeforces.com/) Coding competition website
- [SWE-Bench](https://www.swebench.com/) OpenAI's coding datasets from Github issues

## 1 Multi-Head Latent Attention
MLA was introduced in Deepseek V2 [paper](https://arxiv.org/pdf/2405.04434), and it's **Low Rank Decomposition** for KV cache.
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/mla.png)
You can directly calculate attention from the latent vector C, without restore it back to K/V. That's the because **the upscale matrix $W^{uk}$ can be absorbed into the weight matrix $W^q$**
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/absorb.png)
Query was also low-rank decomposed to save memory
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/q.png)

## 3 Rotary Position Embedding
Two major branches of PE, absolute and relative.
The absolute PE, pro is simple to implement, con is that if the training is short, then the LM can NOT know long position, weak in extensability
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/pe.png)
RoPE is combining both absolute and relative PE. The left figure is the one without PE, middle is adding absolution PE, which can change the vector length. RoPE is designed to maintain vector length, and only applied to Q/K
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/rope.png)
Here is the how RoPE is applied in math, $R_q$ and $R_k$ are rotation for Q, and K, which has the **absolute PE** attributes, and it can be combined as $R$ during attention calculation, which has the **relative PE**attributes.
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/ropemath.png)

But since the RoPE has position information, the K can no long be cached in the MLA schema. We have to decouple RoPE.
From formula, you can see $W^{uk}$ can no long be absorbed by $W^q$ due to the position coupled $R$
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/noabsorb.png)

To solve this issue, we must recompute the keys for all the prefix
tokens during inference, and concat with cached KV.  That's why the RoPE branch shows up in following arch.
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/ropebranch.png)

Another discussion on HF showed the summation is better than concat
![Alt text](/assets/images/2025/25-02-04-DeepseekV3-1_files/sum.png)