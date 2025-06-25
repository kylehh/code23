---
title: OpenAI API v1 change
mathjax: true
toc: true
categories:
  - Application
tags:
  - LLM
---

OpenAI's Dev Day was quite exciting, right? But do you konw they quitely release API v1 and has breaking changes in it? This would keep my busy for next couple of weeks to update documents, example code and cookbooks.

I spent some time exploring what's the new format and how to work with it.   

## 1. With OpenAI class client.  

For `api_key`, you can either set it by environment variable (method 1), or inside the client initialization (method 2)  

For `base_url` , you can NOT set it by environment variable, but have to do it inside client initialization (method 2) or you can do it a hacky method 3 ( NOT recommended, you need to manually make sure a trailing `/` in the URL)
```python
OPENAI_API_BASE="https://api.anyscale.com/v1"

### Method 1 by env. variable
os.environ['OPENAI_API_KEY']=OPENAI_API_KEY
client = openai.OpenAI(base_url=OPENAI_API_BASE)

### Method 2 by client initialization
client = openai.OpenAI(base_url=OPENAI_API_BASE,
 api_key = OPENAI_API_KEY)

### Method 3 hacky way
import httpx
client = openai.OpenAI(api_key = OPENAI_API_KEY)
client._base_url= httpx.URL(OPENAI_API_BASE+'/')

### Get the completions
chat_completion = client.chat.completions.create(...)
```
## 2. Without OpenAI class client.  

The method is most similar to the old API, but make sure to have a trailing `/` in the URL
```python
openai.api_key = OPENAI_API_KEY
openai.base_url = OPENAI_API_BASE +'/'
chat_completion = openai.chat.completions.create(...)
```
## 3. Improvment needed
The pain point is there is NO MORE `OPENAI_API_BASE` environment variable. But according to this [issue](https://github.com/openai/openai-python/issues/745), OpenAI is working on bringing it back with another name. I would guess it will be called `OPENAI_BASE_URL`.  

Adding the trailing '/' is very annoying and I submitted a [PR](https://github.com/openai/openai-python/pull/780) to fix it. But still pending.
