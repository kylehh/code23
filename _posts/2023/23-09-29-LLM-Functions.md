---
title: LLM functions
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

[Open Interpreter](https://github.com/KillianLucas/open-interpreter) was one of the fast growing repos on Github, and it got 26k stars in a month time. I played it in last weekend and had quite some fun with OpenAI LLM. It runs python and shell scripts on my laptop locally and get answers to my questions. But when I tried to swtich to Anyscale Endpoint, job failed.   

I looked into the source code, and found the reason being it uses [LiteLLM](https://github.com/BerriAI/litellm) to call all language models, simplly `litellm.complete()`. It's pretty neat, but somehow AE doesn't work here with proper `api_base` and `api_key`.  

So I looked into LiteLLM source code, this small lib supports multiple LLM endpoints, and it SHOULD work with customzied OpenAI API call. After digging for a while, I realized that it needs `custom_llm_provider` parameter set to `openai` before I was about to create a PR for Anyscale support.   

But Interpreter does not support this parameter, so I submitted a [PR](https://github.com/KillianLucas/open-interpreter/pull/530) for Open Interpreter for adding this parameter and one bug fix.   

Killian, the author of Interpeter replied to me with some questions and discussions. Then he found that there is another solution to LiteLLM without using `custom_llm_provider`, just add a prefix to the model name. Huh, that's easy :)  


OK, here finally leads to LLM functions, or specifically OpenAI functions feature
After fixing the LLM issue, the AE still can't work with Open Interperter, reasoning being `function` role is NOT supported
```
{"given":"function","permitted":["system","assistant","user"]}
```

So what exactly is the function feature in OpenAI? 
I found this simple [tutorial](https://www.datacamp.com/tutorial/open-ai-function-calling-tutorial) and it explains it clearly

## 1. Function is NOT an agent
The function is NOT an agent, which means it won't execute the code and actually do the function call. Instead, the LLM will select a function and output it's arguments. It's this information is correct, you can execute the function call in a proper runtime environment, this is EXACTLY what Open Interperter does in the backend.   
The following example shows these details in `choice` and please notice the `finish_reason` is `function_call`, before it was always `stop`.  
![Alt text](/code23/assets/images/2023/23-09-29-LLM-Functions_files/output_arguments.png)

## 2. Define the Function
How would LLM know which function to call and whats its argument? This knowledge is passed to LLM by defining the `function` parameter. 
Here is one example of defining the function in JSON format.  
![Alt text](/code23/assets/images/2023/23-09-29-LLM-Functions_files/define_function.png)
The interesting part here, is that the `description` is the most important part, so the LLM know what information to extract from the prompt. You can provide as much information as possible here.

## 3. Function execution. 
Eventually you still want to execute the function, right? Since you also know the function name (hopefully it's one of the functino you defined) and argument, just call the function (sample code below)
![Alt text](/code23/assets/images/2023/23-09-29-LLM-Functions_files/function_execution.png)  


Overall, I would say Function feature is one key step to Agent execution. Nice to learn about it. In order to use it in OSS models, fine-tuning are needed to it.