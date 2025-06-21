---
title: SGLang - Nemotron
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Migrated `Llama3.3-Nemontron-Super-49B` support from vLLM to SGLang and submitted [PR](https://github.com/sgl-project/sglang/pull/5073) for it

## 0 Support in vLLM
Deci team recently added Nemotron reasoning model support for the super 48B model. This model is based on `DeciLMForCausalLM` class. This is prunning from Llama 3.3 model which NAS(Network Architecture Search), so it is different from Nano version which is still LlamaCausal LM. 

## 1 Adding model support in SGLang
1. SGLang dev was recommended in the container environment based on `lmsysorg/sglang:dev`. The structure of the container is as following
```shell
/sgl-workspace/sglang # source code dir
/sgl-workspace/work-dir # Mounted for test code 
/root/.cach/huggingface # Mounted for model cache
```
2. The model directory is `/sglang/python/sglang/srt/models`
3. The migration guide is listed [here](https://docs.sglang.ai/references/supported_models.html#port-a-model-from-vllm-to-sglang)
