---
title: Hopfield Network
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Hopfield and Hinton won Noble Prize for Physics this year. Big surprise! I found this [video](https://www.youtube.com/watch?v=1WPJdAW-sFo) explains what's Hopfield's work. It gets me to think how NN is invented, and it's actually much more complicated than it looks.

## 0 How Memoery works 
How do we recall a music melody by just hearing the beginning of yet. Do you search all the brain memoery for a matching pattern? This is surely too slow. Use protein folding as an example,protein get it's proper folding by finding the minimum energy form.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/protein.png)

So the engergy based search method is how Hopefiled get inspired to design the Hopefield Network. Now we need to design a system that can
1. Have a proper energy landscape
2. Can change states for fast search  

![Alt text](/assets/images/2024/24-10-11-Hopfield_files/information.png)

## 1 System Setup
Assume neurons are only **binary** and connections are **symmetics**, which is not how brain is. (A future improvment?)
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/symmetric.png)
We design weights in such a way that it favors alignments when positive
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/excitatory.png)
and favors misalignments when negative
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/inhibitory.png)
Thus, we can calculate the "Happiness" of the networks and the goal is to maximize it.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/happiness.png)
And it's the same as minimize the recip of it
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/goal.png)

## 2 Inference
The inference of this network is to **find all X**, which refers to memory footprint. This is very different from current NN inference.  
In pracitce, we already know part of X, (like hearing the beginning of a melody), and try to recall the rest of it
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/infer.png) 
The update rule can be straightforward.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/updaterule.png)
and we can approve that local and global minimum are the same as long as we have symmetrical connections.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/localglobal.png) 

## 3 Training
The training step is to find all the weights of the network. And the training data is the marked $\xi$ as below, versus $x$ is the general network status.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/trainingdata.png) 
Let's start with one training data case. In order the make the energy of the memory to be lowest, we can just set weight same sign as the product of two neurons connected.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/lowest.png)
This is the call **Hebiann Learning** rule
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/hebbian.png)  
When we have more learning training data, the weight can be set as the summation of of all the weights
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/sum.png)
But each local minimum would interference with each other, thus the number of pattern Hopefield network can be trained on is limited to number of neurons
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/rule.png)

## 4 Future work
Hopfield has improved his work recently. and we will get into Boltzmann machine as another improvement by Dr Hinton.
![Alt text](/assets/images/2024/24-10-11-Hopfield_files/future.png) 

