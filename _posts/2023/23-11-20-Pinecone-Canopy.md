---
title: Pinecone Canopy, tokenizer, poetry...
mathjax: true
toc: true
categories:
  - Application
tags:
  - LLM
---
Pinecone released [Canopy](https://github.com/pinecone-io/canopy), which is a framework for RAG. It original has OpenAI as LLM and embedding model provider and wants to cooperate with Anyscale for open souce LLM support. 

The project was delayed couple fo weeks due to the war situation. Now it's back on track and I submitted PR for AE support and under final reviews.

Couple of things I learnt from this process
## 1. Tokenizer  
You don't realize all the tricks around tokenizers if you simply call Llama2's tokenizer from `Transformers` lib. But initially we don't want to use the heavyweight lib but directly use `Tokenizers` instead, and avoid use gated models like Llama2 which needs HF tokens.   

1. So I tried `OpenLLM/tokenizer`, which gives slightl different tokenized results.

2. `Tokenizers` only loads from a JSON file, while `Transformers` tokenizer classes load JSON, model files. 

3. We finally decided to go back to `from transformers import LlamaTokenizerFast` with `hf-internal-testing/llama-tokenizer`.This is the closes tokenizer we can get for Llama2  
## 2. `pytest` for unit test.
Unit and system tests were added for Canopy, which is a good practice.  
`pytest test.py` to verify the results.    
## 3. `flake8` and `mypy`  
[flake8](https://flake8.pycqa.org/en/latest/) is stype guide enforcement and [mypy](https://mypy.readthedocs.io/en/stable/#) is a static type checker for Python.  
I tried with `black` first and it solves most of the format issues. except for lines being too long. It can be solved with proper `flake8` configs.
## 4. `poetry`  
[poetry](https://python-poetry.org/docs/) is  a tool for dependency management and packaging in Python.  
I didn't get proper `flake8` configs due to installations. Then it solves by `poetry install .` and `peotry run`


![Alt text](/assets/images/23-11-20-Pinecone-Canopy_files/none.png)

