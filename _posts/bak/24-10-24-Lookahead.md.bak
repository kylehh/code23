---
title: Lookahead
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Last blog about Medusa was going longer than I expected. So I will write separete blogs about lookahead and EAGLE1/2

Lookahead is from LMSYS, and I mainly read from this [blog](https://lmsys.org/blog/2023-11-21-lookahead-decoding/) and this [post](https://zhuanlan.zhihu.com/p/675406771)

## 1 Jacob Iteration Algorithm
This is an algorithm to solve nonlinear equations iteratively. Details [here](https://en.wikipedia.org/wiki/Jacobi_method). It breaks down the matrix to a diagonal one (easy to revert it by taking reciprocal of all diagonal elements) and L/U.
![Alt text](/assets/images/2024/24-10-24-Lookahead_files/jacobmatrix.png)

The idea is to change auto-regression decoding into a nonliear equations system and solve all the tokens by iteration
![Alt text](/assets/images/2024/24-10-24-Lookahead_files/jacobauto.png)

## 2 Lookahead decoding
So we can solve all m tokens in k iterations with **parellel decoding**. If $k<<m$ then we can achieve speed up with lookahead decoding. The paper is [here](https://arxiv.org/pdf/2305.10427)
![Alt text](/assets/images/2024/24-10-24-Lookahead_files/decodep.png)