---
title: Anyscale Endpoint Integration - LangChain
mathjax: true
toc: true
categories:
  - Application
tags:
  - LLM
---

First thing first, it took me couple of commits to pass the formatting check in this [PR](https://github.com/langchain-ai/langchain/pull/11569) with `ruff` and `black`, so I'd better to record them down first
1. Ruff
```
pip install ruff
ruff check .
ruff check --fix .
```
2. black
```
pip install black
black filename.py
```
Previously LangChain would show some recommendation to run code through these two tools to avoid lint failure. It somehow didn't show now, so it's till good to try with these two tools before commit your changes.  

Ok, I initially add Anyscale Service support into `langchain.llms.anyscale` before Anyscale Endpoint was created ([PR](https://github.com/langchain-ai/langchain/pull/4350) here). There are some small LLMs like `lightGPT` can be hosted under Anyscale Service so I did a weekend project to add the integration into LangChain with REST API calls.  

After AE was released and `langchain.llms.ChatAnyscale` was added, most of examples are use `ChatAnyscale` class to interact with Llama2 models. 

But the chart [here](https://python.langchain.com/docs/integrations/llms/) make Anyscale look incomplete which only support `invoke` feature. 

So here are some challenage and progress of updating `langchain.llms.anyscale` to work with AE.

1. LC LLMs are supposed to be used for QA models, so it directly takes string format prompt as input, instead of role/content format for chat models. Since AE only hosts chat models for now, we need to modify string format prompt to chat model format and also modify chat model output as well.
2. OpenAI has both QA and chat models, in order to make them all work with LC LLMS, there are OpenAI class and OpenAIChat class inherited from BaseOpenAI. So we need to combine these two classes to make our Anyscale class. 
3. OpenAI class implementation supports batch and async batch features, that’s because QA models can take in a batch of prompts in the format of List[str], which is NOT supported by Chat models. ( There is a hacky workaround [here](https://community.openai.com/t/batching-with-chatcompletion-endpoint/137723), but the output is not stable, especially for Llama2 models)
4. There are some other OpenAI supported parameters, like n (number of completions) which is NOT supported by AE.


  

