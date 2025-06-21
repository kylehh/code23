---
title: Andrej Karpathy-MakeMore
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

2.5 hr video of [MakeMore](https://www.youtube.com/watch?v=PaCmpygFfXo&list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ&index=2).

## 1. Python tricks
- Counter with dict without if/else  
  ```python  
  d = {}
  d['key'] = d.get('key', 0) + 1
  ```
- Set random seed in Torch
  ```python
  # Fixed rand by manual seed
  g = torch.Generator().manual_seed(2147483647)
  p = torch.rand(3, generator=g)
  p = p / p.sum()
  p
  # sampled from the p distribution
  torch.multinomial(p, num_samples=100, replacement=True, generator=g)
  ```
- Broadcasting semantics
  ```python
  # Line up dims from the trailing dim, and check if they are either
  # - equal
  # - 1  (will be duplicated to match)
  # - none (will be changed to 1, then match)
  P = (N+1).float() # 27x27
  P /= P.sum(1, keepdims=True) #27x1
  # Following operation is wrong
  # line up the last dimention FIRST
  # (27x27)/(27) -> (27x27)/(1,27)
  P /= P.sum(1) #keepdims default=False
  ```
- Plot a square figure  
  ```python  
  plt.imshow(N, cmap='Blues')
  for i in range(dim):
      for j in range(dim):
          plt.text(j, i, 'strb', ha="center", va="bottom", color='gray')
          plt.text(j, i, 'strt', ha="center", va="top", color='gray')
  ```   

## 2 Neural Network Setups
- Maximum Likehood Estimation

  GOAL: maximize likelihood of the data w.r.t. model parameters (statistical modeling)  
  equivalent to maximizing the log likelihood (because log is monotonic)  
  equivalent to minimizing the negative log likelihood  
  equivalent to minimizing the  average negative log likelihood  
  ```python
  for ix1:
    for ix2:
      prob = P[ix1, ix2]
      logprob = torch.log(prob)
      log_likelihood += logprob
  nll = -log_likehood
  ```  
- One hot encoding  
  Convert `one_hot` to `float()`!  
  ![Alt text](/assets/images/2024/24-04-17-Karpathy-makemore_files/oneshot.png)

- logits and softmax
  ```python
  # 5x27 @ 27x27 -> 5x27
  logits = xenc @ W # Interpreate as log-counts
  counts = logits.exp() # counts table, equivalent N
  probs = counts / counts.sum(1, keepdims=True) #normalized counts table
  ## the last 2 lines are actually softmax :)
  ```
- loss and weight update  
  This is similar to MicroGrad  
  ```python
  # retrieve 5 prob values from probs table
  loss = -probs[torch.arange(5), ys].log().mean()
  # backward pass
  # W = torch.randn((27, 27), generator=g, requires_grad=True) # set True for required_grad
  # W.grad.shape == W.shape
  W.grad = None # set to zero the gradient
  
  loss.backward() # cals all the W.grad
  # update weights
  W.data += -0.1 * W.grad
  ```

## 3 Notes
- The weights $W$ eventually will converge to the log counts table $N$ b/c no more information we get from the NN rather than counts
- Adding the counts `P = (N+1).float()` would leads to smooth of the distribution. 
- Add regularization term `0.01 * w**2.mean()`