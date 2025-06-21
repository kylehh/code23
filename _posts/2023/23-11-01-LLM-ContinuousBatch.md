---
title: Continuous Batch
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Read the [blog](https://www.anyscale.com/blog/continuous-batching-llm-inference) about continuous batching, from Cade and Shen.  

1. **The initial ingestion of the prompt takes about as much time as the generation of each subsequent token**. No wonder the prompt side does NOT affect the latency.

2. LLM inference is **memory-bound** calculation.

3. 13B model requires 13x2=26 GB for model weights, according to [Numbers every LLM developer should know](https://github.com/ray-project/llm-numbers#1-mb-gpu-memory-required-for-1-token-of-output-with-a-13b-parameter-model), it's 2x factor here. and surprisingly, **each token consumes 1MB**. So for a 40GB A100, there is 40-26=14GB left after model hosting, and it's only 14K tokens. You can only limit batch size to 7 for 2048-token-sequence.

4. Traditional static batch shown below. Early finished sequence have to wait for late finished seq and cause unutilized GPUs.
![Alt text](/assets/images/2023/23-11-01-LLM-ContinuousBatch_files/staticbatch.png)

5. Continuous batching: Once a sequence emits an end-of-sequence token, we insert a new sequence in its place. TGI includes this algo in its implementation.
![Alt text](/assets/images/2023/23-11-01-LLM-ContinuousBatch_files/continuousbatch.png)

6. PagedAttention and vLLM:   They allow the **KV cache to be non-contiguous** by allocating memory in fixed-size “pages”, or blocks. The attention mechanism can then be rewritten to **operate on block-aligned inputs**, allowing **attention to be performed on non-contiguous memory ranges.**. Well, I will write another blog about vLLM in more details when I understand better. 


