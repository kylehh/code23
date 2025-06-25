---
title: LLM Router
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

[LLM Router](https://lmsys.org/blog/2024-07-01-routellm/) was introduced by LMSYS and Anyscale. The first open sourced LLM routing and introduced 4 different routing policies.

0. Overview
The LLM Routing can be used for every GenAI workflow as the first step to choose a proper model.
In paper it introduced:
- Similarity Weighted, it uses public data to from Chatbot Arena, calculates simiarity from prompt and use it as weight to calcualte BT score to choose models
- Matrix Factorization learns a scoring function for how well a model can answer a prompt
- A BERT classifier that predicts which model can provide a better response, but only trains on the last layer.
- A causal LLM classifier that also predicts which model can provide a better response, but training the whole model.

1. Similarity Weighted
A good starting point is this [Colab Notebook](https://colab.research.google.com/drive/1KdwokPjirkTmpO_P1WByFNFiqxWQquwH#scrollTo=mukqgshMarFi). It shows how the BT is calculated from [arena datasets with win/loss] (https://storage.googleapis.com/arena_external_data/public/clean_battle_20240814_public.json) 
(This JSON keeps updating, from 20240730 to 20240814). 
It contains 1799991 (After dedup:  1670250)

The model is covers are listed below
```python
array(['chatglm-6b', 'oasst-pythia-12b', 'koala-13b', 'vicuna-13b',
       'stablelm-tuned-alpha-7b', 'alpaca-13b', 'llama-13b',
       'dolly-v2-12b', 'fastchat-t5-3b', 'gpt-3.5-turbo-0314',
       'gpt-4-0314', 'claude-1', 'RWKV-4-Raven-14B', 'mpt-7b-chat',
       'palm-2', 'claude-instant-1', 'vicuna-7b', 'wizardlm-13b',
       'gpt4all-13b-snoozy', 'guanaco-33b', 'vicuna-33b', 'mpt-30b-chat',
       'gpt-3.5-turbo-0613', 'gpt-4-0613', 'llama-2-7b-chat',
       'claude-2.0', 'llama-2-13b-chat', 'chatglm2-6b',
       'llama-2-70b-chat', 'codellama-34b-instruct', 'wizardlm-70b',
       'falcon-180b-chat', 'mistral-7b-instruct', 'qwen-14b-chat',
       'zephyr-7b-alpha', 'zephyr-7b-beta', 'openchat-3.5',
       'gpt-4-1106-preview', 'gpt-3.5-turbo-1106', 'chatglm3-6b',
       'claude-2.1', 'tulu-2-dpo-70b', 'yi-34b-chat',
       'starling-lm-7b-alpha', 'openhermes-2.5-mistral-7b',
       'pplx-70b-online', 'pplx-7b-online', 'dolphin-2.2.1-mistral-7b',
       'mixtral-8x7b-instruct-v0.1', 'gemini-pro',
       'solar-10.7b-instruct-v1.0', 'mistral-medium',
       'llama2-70b-steerlm-chat', 'gemini-pro-dev-api',
       'stripedhyena-nous-7b', 'bard-jan-24-gemini-pro',
       'deepseek-llm-67b-chat', 'gpt-4-0125-preview',
       'gpt-3.5-turbo-0125', 'nous-hermes-2-mixtral-8x7b-dpo',
       'mistral-7b-instruct-v0.2', 'qwen1.5-72b-chat',
       'openchat-3.5-0106', 'qwen1.5-4b-chat', 'qwen1.5-7b-chat',
       'codellama-70b-instruct', 'mistral-next', 'gemma-7b-it',
       'mistral-large-2402', 'gemma-2b-it', 'olmo-7b-instruct',
       'claude-3-sonnet-20240229', 'claude-3-opus-20240229',
       'claude-3-haiku-20240307', 'starling-lm-7b-beta', 'command-r',
       'dbrx-instruct-preview', 'qwen1.5-14b-chat', 'qwen1.5-32b-chat',
       'command-r-plus', 'gemma-1.1-7b-it', 'gpt-4-turbo-2024-04-09',
       'gemma-1.1-2b-it', 'zephyr-orpo-141b-A35b-v0.1',
       'gemini-1.5-pro-api-0409-preview', 'reka-flash-21b-20240226',
       'reka-flash-21b-20240226-online', 'mixtral-8x22b-instruct-v0.1',
       'llama-3-8b-instruct', 'llama-3-70b-instruct',
       'phi-3-mini-128k-instruct', 'snowflake-arctic-instruct',
       'qwen1.5-110b-chat', 'reka-core-20240501', 'qwen-max-0428',
       'gpt-4o-2024-05-13', 'yi-large-preview', 'glm-4-0116',
       'phi-3-mini-4k-instruct', 'gemini-advanced-0514',
       'gemini-1.5-pro-api-0514', 'gemini-1.5-flash-api-0514',
       'yi-1.5-34b-chat', 'phi-3-small-8k-instruct',
       'phi-3-medium-4k-instruct', 'qwen2-72b-instruct', 'yi-large',
       'nemotron-4-340b-instruct', 'reka-flash-preview-20240611',
       'glm-4-0520', 'deepseek-coder-v2', 'claude-3-5-sonnet-20240620',
       'gemma-2-27b-it', 'gemma-2-9b-it',
       'phi-3-mini-4k-instruct-june-2024', 'deepseek-v2-api-0628',
       'athene-70b-0725', 'gemini-1.5-pro-exp-0801',
       'gpt-4o-mini-2024-07-18', 'deepseek-coder-v2-0724',
       'gemma-2-2b-it', 'llama-3.1-405b-instruct',
       'llama-3.1-70b-instruct', 'llama-3.1-8b-instruct',
       'mistral-large-2407', 'reka-flash-20240722', 'reka-core-20240722',
       'chatgpt-4o-latest', 'gpt-4o-2024-08-06'], dtype=object)
```

But not sure if LMSYS would open the prompt/response for this datasets. The open sourced embedding are created by OpenAI Embedding model with 1536 length, which is not a general length. 

The update I added was
1. expand from 2 model selecion to multi-model selection
2. I think the current 2 model has a bug when 2 model's order are exchanged, will provide more details