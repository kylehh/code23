---
title: Sliding Window Attention
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

I was debugging a sliding window attention bug and it was fixed by this [PR](https://github.com/vllm-project/vllm/pull/17180). I helped on the review and get it merged. 

## 1 Sliding Attention
Sliding window attention is from [Longformer paper](https://arxiv.org/pdf/2004.05150) is limiting the **receptive field** of attention layer, so each token is only getting attentions from previous w tokens.
It can also integrate w dilate concept similar to CNN.
And also combined w full/global attention for some special tokens, and local attention for the rest of the token.
![Alt text](/code23/assets/images/2025/25-04-27-sliding_files/slide.png)

## 2 Interleaved Sliding window
I can't find the original paper for this idea but the concept is that one layer for full attention and one other layer for sliding window. 
So the [implementation](https://github.com/huggingface/transformers/blob/54be2d7ae87e873482b984cc956e165ca4dc0ba3/src/transformers/models/gemma2/modeling_gemma2.py#L312) on HF is like following:
```
self.sliding_window = config.sliding_window if not bool(layer_idx % 2) else None
```
## 3 vLLM Gemma2/3
1. FlashInfer backend  
- Non-uniform sliding window isn't supported for **flashinfer** v0 or v1. So interleaved attention is not supported
- So for interleaved attention model, like gemma2/3, Disabling sliding window and capping "the max length to the sliding window size": `max_model_len<=sliding_window_size`
- To test, run `VLLM_ATTENTION_BACKEND=FLASHINFER VLLM_ALLOW_LONG_MAX_MODEL_LEN=1 python3 examples/offline_inference/vision_language.py --model gemma3`. We need to set `VLLM_ALLOW_LONG_MAX_MODEL_LEN` because `max_model_len` is set larger than `sliding_window_size`
2. Flash_Attention backend  
- For interleaved attention in **flashatten**, when `sliding_window` is none, just set `local_attn_masks` to be none. 
- This is the interleaved attention version.`VLLM_ATTENTION_BACKEND=FLASH_ATTEN python3 examples/offline_inference/vision_language.py --model gemma3` 

