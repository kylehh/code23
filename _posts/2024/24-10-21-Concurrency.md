---
title: Concurrency Execution
mathjax: true
toc: true
categories:
  - Code
tags:
  - Python
---

I was testing sending concurrent requests to LLM server and would like to record two ways to running concurrent processes. and would have a deeper dive on async later

## 1 With `concurrent` lib
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
def llm_call(idx):
    response = requests.post(url, json=json_data, headers=headers)
    return(idx, response.json()['choices'][0]['message']['content'])

with ThreadPoolExecutor() as executor:
    #executor.map(llm_call, ints)
    futures = [executor.submit(llm_call, i) for i in range(Test_number)]

    # Retrieve results as they become available
    for future in as_completed(futures):
        try:
            idx, result = future.result()
            #print(f"Result: {idx}, {result}")
        except Exception as e:
            print(f"Error: {e}")
```

## 2 With `asyncio` lib
```python
import asyncio

async def call_private_api_client():
    answer = ...
    return answer

def llm_call(idx):
    result = asyncio.run(call_private_api_client())
    return(idx, result)

async def llm_call_gather(num):
    tasks = [call_private_api_client() for _ in range(num)]
    return await asyncio.gather(*tasks)

if __name__ == "__main__":
    resp = llm_call(0)   
    results = asyncio.run(llm_call_gather(num_con_tests))
```
