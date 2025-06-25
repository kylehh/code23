---
title: Poe and Modal
mathjax: true
toc: true
categories:
  - Application
tags:
  - LLM
---

Poe, a chatbot hosting service backed by Quora, is getting popular.
I tried to add AE as one of the Chatbot, with Zephyr 7B model. (It seems that first model to host Zephyr 7B on Poe)  
![Alt text](/code23/assets/images/2023/23-11-15-Poe-Modal_files/poebot.png)
Couple of findings
## 1, Hosted on Modal
Finally I got a chance to try out Modal. Heard about this name many times and never really know that it is about. The offical guide for Poe is using Modal as its host server. 

I followed the steps to sign up on Modal, but somehow got stuck at the billing step (my credit card issue). Somehow my registration is stuck and didn't get login till the next day.

After I got the token, creating a hosting environment and uploading my service code is done easily. So easy that make me all the sudden realize how many more steps we needed to make it run on Anyscale. Of course, it's not a fair comparion b/c Modal is host everything in its cloud, versus Anyscale deploy into your own cloud, but for simple use cases, Modal wins as a infra-as-a-python-code service.   
`modal deploy main_modal.py  # For deployment`  
`modal serve main_modal.py   # For dev testing`  
![Alt text](/code23/assets/images/2023/23-11-15-Poe-Modal_files/modal.png)
## 2. Poe Bot
Poe bot server is very straightforward, consider it as a broker between users and our own bot hosted on Modal. Poe would provide all the interface and message packing between users and our own bot, and in fact, our own bot is nothing but converting between Poe messages into OpenAI format and call OpenAI client using AE `base_url` and `api_key`
![Alt text](/code23/assets/images/2023/23-11-15-Poe-Modal_files/poe.png)

## 3. To do
1. Will try to replace Modal with Ray Serve or Anyscale Service
2. Add streaming
3. Make it more robust. Current code is already working but [Fireworks](https://github.com/fw-ai/fireworks_poe_bot) is doing quite some more edge case handling

## 4. Thoughts
1. I see some interesting bots there, especially for image gen.
2. Someone creates dozens of bots, but I guess they are using the same or very similar models
3. It can be monetized, and that's why people are spamming it with bots
4. Seems hosting on Modal is free for Poe? Or just my clicks are so low and still in free tier.  