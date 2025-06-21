---
title: Tensor Parallelism and Pipeline Parallelism
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

There are multiple parallelism strategies from [video](https://www.youtube.com/watch?v=hpiuWCcUDPo)

## 1 Data Parallelism
This basic parallelism is split data between GPUs
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/data.png)
In the LLM era, model parallelism are actually used
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/model.png)
Callout **NVLink between GPUs** and **InfiniBand between nodes**.
**SXM** version GPU are GPU with NVLink connections, instead of PCIe
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/nvlink.png)

## 2 Pipeline Parallelism
Also call inter-layer parallelism. (**inter-** means between)
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/pp.png)
It has **bubbles** so Google [paper](https://arxiv.org/pdf/1811.06965) introduced micro-batch to mitigate the time waste.
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/bubble.png)
NVidia introduced 1F1B in this [blog](https://developer.nvidia.com/blog/scaling-language-model-training-to-a-trillion-parameters-using-megatron/)
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/1f1b.png)

## 3 Tensor Parallelism
Also call intra-layer parallelism. (**intra-** means within)
Matrix calculation can be divided, so it leads to TP algorithm
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/tp.png)

Examples can be found in the Megatron-LM [paper](https://arxiv.org/pdf/1909.08053)
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/megatron.png)

## 4 3D Parallelism
All these method can be used together and that's the idea behind **Deepspeed**.
![Alt text](/assets/images/2025/25-02-08-TPandPP_files/3d.png)