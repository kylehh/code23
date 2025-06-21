---
title: Disagg PD in vLLM and LMCache
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Tested out Disagg PD in vLLM and sth about [LMCache](https://blog.lmcache.ai/), an open-source **Knowledge Delivery Network (KDN)**, and the Redis for LLMs. 

## 1 Disagg PD in vLLM v0
Disagg PD in v0 is just a naive implementation using NCCL. 
1. It's not fully integrate with vLLM serving, and a proxy server was used in the example to forward request from prefill to decode
2. The proxy server can not handle genai-perf requests properly and no perf results were generated
3. The serving is unstable, and it would exist after some torch distribution timeout

The serving [script](https://github.com/vllm-project/vllm/blob/main/examples/online_serving/disaggregated_prefill.sh) provided by vLLM basically does following things:  
1. starts two vLLM servings
2. launches the [proxy server](https://github.com/vllm-project/vllm/blob/main/benchmarks/disagg_benchmarks/disagg_prefill_proxy_server.py).
3. For prefill request, changes `max_tokens` to be 1, and send request to prefill
4. Send the orginal request to decode. 

## 2 LMCache PD in vLLM v1
LMCache integrates with vLLM v1 and supports NIXL for ultra-fast KV cache transfer. Details in this [blog](https://blog.lmcache.ai/2025-04-11-lmcache-vllmv1-nixl/) and benchmark in this [blog](https://blog.lmcache.ai/2025-04-29-pdbench/)  
![Alt text](/assets/images/2025/25-05-02-LMCache_files/vllm.png)
Key takeaways:  
1. LMCache batches the KV blocks into a single large buffe
2. Smaller blocker (default 16 tokens), leads to many tiny transfers and underutilizing network
3. Larger blockers, reduces prefix caching hit rates, and fragment GPU mem (opposite of paged attention philosophy)
4. LMCache decoupled buffering step solves this issue. 
![Alt text](/assets/images/2025/25-05-02-LMCache_files/buffer.png)
Detailed steps are listed in LMCache [doc](https://docs.lmcache.ai/getting_started/quickstart/disaggregated_prefill.html)