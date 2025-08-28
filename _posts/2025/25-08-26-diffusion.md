---
title: Flow and Diffusion models Part 2
mathjax: true
toc: true
categories:
  - Study
tags:
  - VLM
---

[Lecture 2](https://www.youtube.com/watch?v=VDnM5D6wXio&t)
This is the most mathmatical chanllenging part of the lecture, but also covers all the missing knowledge I would like to learn, like Langevin and Fokker-Planck

## 1 Training goal
Review two models we learned so far
![Alt text](/code23/assets/images/2025/25-08-26-diffusion_files/2models.png)
Learning goal is to find the formula for the target vector field such taht corresponding ODE/SDE convert $p_init$ to $p_data$
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/traininggoal.png)

Two key concepts
- conditional: per single data point
- marginal: across distribution of all data points

## 2 Probability Path
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/probpath.png)
For conditional probability path
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/cpp.png)
We have a example of Gaussian prob path with noise schedule s.t
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/gpp.png)
For marginal probablity path, this is a bit hard to digest. The key idea is to understand probability path as **a trajectory in the space of distributions**. 
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/mpp.png)
The density $p_t(x)$ is unkonw because the integral is intractable.

## 3 Vector Field
If we simulate a ODE with a conditional vector field, then any state on the trajectory is following conditional probabilty path. 
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/cvf.png)
The Gaussian example is given by 
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/gvf.png)
The Marginalization Trick, which can be approved by Continity equation, 
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/mvf.png)

Here is the comparison of these two under ODE samples
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/odesamples.png)

## 4 Continuity Equation
CE can be interpreated by the conservation of probability mass. 
![Alt text](/code23/assets/images/2025/25-08-21-diffusion_files/ce.png)
From this equation, we can derive the marginalization trick