---
title: Flash Attention
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

It's time to dig into some LLM optimization algirthms. My first googled question was "Flash Attention vs Paged Attention", which are two popular optimizations. Here are some quick points.  
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/title.png)
a. [Flash Attention](https://arxiv.org/abs/2205.14135) paper is from Stanford, and key contributor Tri Dao is with Together.xyz right now.  
b. [Paged Attention](https://arxiv.org/abs/2309.06180) is from Berkeley Ion's group, and implemented in [vLLM](https://vllm.readthedocs.io/en/latest/).

Flash Attention is a very efficient algo with hardware level optimization, the you can barely know nothing about it from the graph below, even though it's quoted in everywhere.  
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/graph.png)  
I found an excellent [Zhihu page](https://www.zhihu.com/question/611236756) to explain it, in great detail and clear logic. I can simply quote is here without any more explanation. The notes below are for self reminding tips.
1. The whole idea of FA is to reduce MAC(Memory Access Cost) at the cost of FLOPS. Try to do more calculations in SRAM(Static RAM) rather ine HBM(High Bandwidth Memory).    
2. The key idea to imporve Self-Attention is to do calculation in blocks. All matrics calculations are easy to break down into blocks but the Softmax is not straightfoward. Let's take a look how to optimize it.  
3. Here is the original **stable** version of softmax formular. (Form 10 is stablization)
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/softmax.png)
4. We are break it down into two blocks and the way to calculate local softmax for block 1 is the same, which is actually showned above. Now the question is now to update the global results when we add the second block. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/addsecond.png)
5. Update parameters when block 2 local calculation is done. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/updatesecond.png)
6. There are more details to explain formula 18, which is straightforward and I will skip here. Now you can update the global software max. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/updateglobal.png)
7. Put everything together, here is the final formula in the code. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/finalformula.png)
See code line 12 for the key update. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/code.png)
8. Why FA is faster? Let's see what's the original calculation cost. 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/whyfast1.png)9. With FA and the assumption that M (on-chip SRAM size) >> d (head dimemsion)
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/whyfast2.png)
10. For a more straightforward view, the NxN attentions score matrix is breakdown into batches to calculate local softmax
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/batch.png)
11. The rows are actually only for batch processing purpose. Author simplified to 1 row and how exactly softmax are calculated locally(with multiple columns). 
![Alt text](/assets/images/2023/23-10-30-LLM-FlashAttention_files/blockbyblock.png)

Levargeing GPU and manipulate different memory on GPU are something we did lots when optimization RTM(Reserve Time Migration) in CGG. Glad to read this paper and see ML is getting into this black magic field!