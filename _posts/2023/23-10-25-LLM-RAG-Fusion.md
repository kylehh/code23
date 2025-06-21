---
title: RAG Fusion
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

This is a typical example of we can enrich RAG with more advanved methods, and it does NOT required more complicated algorithm or theories. [RAG Fusion](https://towardsdatascience.com/forget-rag-the-future-is-rag-fusion-1147298d8ad1) is very straightforward and I can see it also imporve the RAG results in an obvious way. 

A cookbook implementatin is [here](https://github.com/anyscale/endpoint-cookbook/blob/main/App_RAG_Fusion.ipynb).

![Alt text](/assets/images/2023/23-10-25-LLM-RAG-Fusion_files/RAGFusion.png)

Three steps in RAG Fusion

1. Generate multiple queries based on the original query. GPT-3.5 output multiple queries in the right format -- List without any extra verbose. So I created a dataset with FireAct method to finetune the Llama2 and achieve similar output.  

This should be done simply by prompt engineering but no luck so far

2. Reciprocal Rank Fusion([RRF](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf)). Well, it's a better ranking algorithm than any individual system, according to the paper.

3. I was confused by the last step, by thinking we should use the weight data and all the RAG context from all the queries. Actually just need to pick **top_k** results and work as normal RAG. The weights are never used, could be a future research direction. 

4. It's officiallay supported in LlamaIndex now! 

