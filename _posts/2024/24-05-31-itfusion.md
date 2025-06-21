---
title: Image Text Fusion
mathjax: true
toc: true
categories:
  - Study
tags:
  - MultiModals
---

Jump into Multi Models before June. 
This [video](https://www.youtube.com/watch?v=vRy4K4rs350) talks 6 different ways to fuse text and image together.

Before the summary of 6 methods, two paper were called out for examples 

0. ViLT 
Visual and Language Transformer shows the role of text embedding, visual embedding and modality integration. There three parts are the basis of multi modal models 
![Alt text](/assets/images/2024/24-05-31-itfusion_files/vilt.png) 
The implementation in ViLT is as follow, just concatentate VE an TE into transformers
![Alt text](/assets/images/2024/24-05-31-itfusion_files/vilt2.png) 

1. LlaVa
Large Language-and-Vision Assistant. I believe there will be other blogs about this paper. 
The key difference here is a projection matrix $W$ to map video encoding same size as the language encoding. 
![Alt text](/assets/images/2024/24-05-31-itfusion_files/llava.png) 

2. Summary
From these two examples, we already can see these two patterns of embedding fusion. So here are the list of all 6 common patterns. 
The first 3 are very straightforward.
![Alt text](/assets/images/2024/24-05-31-itfusion_files/123.png) 
The other three are listed below, 2 are transformer based.
![Alt text](/assets/images/2024/24-05-31-itfusion_files/456.png) 
The last one is about creating cross attention between text and image
![Alt text](/assets/images/2024/24-05-31-itfusion_files/6.png)
and sum them together
![Alt text](/assets/images/2024/24-05-31-itfusion_files/66.png)  