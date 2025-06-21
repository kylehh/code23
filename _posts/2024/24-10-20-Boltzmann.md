---
title: Boltzmann Machine
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

I started learning ML with Andrew Ng's course, and at the same time, I also took Neuron Network from Hinton. The second one is actually very hard for me and I never really understand the Boltamann Machine concept, until this [video](https://www.youtube.com/watch?v=_bqa_I5hNAo). It follows Hopefiled introduction and clearly explained how we add stochasticity and hidden units to achive generativity fron MM.

## O The beginning
All the generative idea can be tracked back to Boltzmann Machine. Other than Hopfield which can perfectly restore memoery, which, however, is limited by number of neuron, Boltzmann focuses on generate patterns never seen before.
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/beginning.png)

## 1 Ludwig Boltzmann
In 1870, Boltzmann got his PhD from University of Vienna. His contriution to thermodynamics is to find the probability of a particular's status with energy E.
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/boltzmanne.png)
The deduction is straighforward 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/deduction.png)
Given the condition that all prob add to be 1, we can notice that the absolute energy is proportional to the relative energy. So we can use the same formular with **partition function** as the scalar and get the full **Boltzmann Distribution**
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/distribution.png)
To summary here
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/summary.png) 

## 2 Inferences.
Let's recall how Hopfield update the status for inference. 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/hopfield.png)
and Boltzmann introduce non-deterministric update 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/inference.png)  
Here are the details of updating deduction
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/update.png) 
Replace $\Delta_E$ , you will get the **Sigmoid function** naturally
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/deltae.png) 
This is also explains the role of temperature, which is widely used in LLM, and low temp means more deterministric results, sigmoid degraded into a step function. 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/temp.png) 
So here is the summary of inerence steps.
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/steps.png) 

## 3 Training
Boltzmann needs to learn the underlying distribution of the training data, istead of just memorizing the specific examples. This is the previous **energy based** learning object not pactical here
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/notworking.png) 
The new objetive is all the general goal of all generative ML methods
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/objective.png) 
Through some math deductions, you will get following formula. The minimize data part is easy to interpreate, which is to low the energy of training data, same as Hopefield machine. 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/miniz.png) 
But the second part is very interesting. Since majority of the status are jiberish, so minimize the partition function, is to maximize the energy of jibberish status (due to the nagative sign). 
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/training.png) 
This leads to the **contrastive learning** rule due to the contrastive terms. I see contrastive a lot in the Diffusion learnings, and here is the origin!
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/contrastive.png) 

## 4 Hidden Neurons
Adding hidden neurons, which are not connected to inputs. This is crucial for network to learn latent features instead of directly memorizing the examples. The question is how to train them if they are not connected to the training data. The contrastive learning method also adapts here.
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/hidden.png) 

## RBM
The restriction, is to restrict the connection between visible, or hidden neurons. Only visible to hidden connections are allowed
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/rbm.png) 
The benefit of doing so is that we can update neurons layer by layer
![Alt text](/assets/images/2024/24-10-20-Boltzmann_files/benefit.png) 

