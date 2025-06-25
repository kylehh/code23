---
title: SmoothQuant and AWQ
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

GO through LLM Quantization technologies, mainly from Han's group in MIT

## 0 Quantization basic
Quantization is nothing more about scaling. After getting the range of the original FL32 data, by **calibration**, we scale down the data and remap to a low bits range. 
$$
Q(w)=\Delta*Round(\frac{w}{\Delta}) \\
\Delta= \frac{\max{(w)}}{2^{N-1}}
$$
Another concept is **activation**, which is actually input $X$ instead of activation functions. In $Q=W_qX$, $X$ is the activation of weight $W_q$

## 1 SmoothQuan
Han introduced this method in this [video](https://www.youtube.com/watch?v=U0yvqjdMfr0) and [zhihu](https://zhuanlan.zhihu.com/p/703928680) and [paper](https://arxiv.org/pdf/2211.10438) are very helpful as well. 

One formula explained all  
$Y=(Xdiag(s)^{-1})(diag(s)W)=\hat{X}\hat{W}$

The key challenge is that activation has larger dynamic range and hard to quantize.
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/outliners.png)
So instead of per-tensor quantization, we can consider per-token and per-channel quantization. 
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/pertoken.png)
The outliners are mainly concentrated in certain **channels**. So we can shift them into weights.  
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/channels.png)

## 2 AWQ
This is 4-bit quantization also from Han's group, and here are Han's [talk](https://www.youtube.com/watch?v=3dYLj9vjfA0), [zhihu(really good explanations)](https://zhuanlan.zhihu.com/p/697761176), and [paper](https://arxiv.org/pdf/2306.00978)

The goal is to get weight only quantization for single-batch LLM performance. (W8A8 is only good for batch serving and not enough for single-query LLM inference)
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/w4a16.png)

1. Only 1% of salient weight is important for the results. and the paper found out choosing the salient weight based on *weight* is similar to *random* choosing. So **Activation-aware** selection method is used. 
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/salientweight.png)

2. The paper **noticed** that scale up the salient weight and reduce the quantization error, which is a key contribution.
Here is the induction:
Similar to SmoothQuan, we can scale up weight and scale down the activation  
$$
Q(w*s)x/s=\Delta^\prime*Round(\frac{w*s}{\Delta^\prime})*x/s \\
\Delta^\prime=\frac{\max{(w*s)}}{2^{N-1}}
$$

Based on **empirical** findings, the error is propotional to $\frac{1}{s}$
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/empirical.png)
and a test shows s=2 gives the best result while larger s would increase non-salient weight error
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/s2.png) 

3. The calculation of the scaling factor can NOT use SGD due to **round** functino is not differentiable. 
A grid search is used here for a simplied factor $\alpha$
The source code can be found [here](https://github.com/mit-han-lab/llm-awq/blob/52d3c26631bf62810bf4d4ab30e43d5b07818a38/awq/quantize/auto_scale.py#L124C1-L131C67)  

```python
 n_grid = 20
history = []

org_sd = {k: v.cpu() for k, v in block.state_dict().items()}
for ratio in range(n_grid):
    ratio = ratio * 1 / n_grid
    scales = x_max.pow(ratio).clamp(min=1e-4).view(-1)
    scales = scales / (scales.max() * scales.min()).sqrt()
```

You can see that the ratio $\alpha$ is searched between 0 and 1 with a step of 0.05.
and another scaler is added 
$s = \frac{s}{\sqrt{max(s)min(s)}}$

4. `group_size` is a hyperparameter used to share $\alpha$ between number of channels.
It's also shows as `INT3_group128` which means 128 channels shares a same scaling factor

So here is the summary of the process
![Alt text](/code23/assets/images/2025/25-03-18-Quantization_files/awq.png)


