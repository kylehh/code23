---
title: Kaiming's ML overview at MIT
mathjax: true
toc: true
categories:
  - Study
tags:
  - ML
---
Kaiming He joint MIT as associate professor in Feb 2024 and deliveried "Deep Learning Bootcamp" as his first public talk as MIT professor. It's very pleasant to go over his 1hr talk and here are some notes

DL is all about repensentation.  
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/representation.png)
LeNet was firstly introduced in 1989
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/lenet.png)
Conv is local connections with weight sharing.
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/conv.png)
Pooling is about local invariance
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/pool.png)
AlexNet in 2012 is big break through after 20yrs.
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/alexnet.png)
The net goes wider for more features
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/wider.png)
and used ReLU for better gradient prop. And used "dropout"
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/relu.png)
Finally I understand how the visualization works-- by back prop a one-hot feature map to pixels!!!
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/visualization.png)
Single most important finding, wow, didn't think in this way before.
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/transferable.png)
VGG in 2014, too deep to train
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/vgg.png)
Let's see why from initilization 
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/init.png)
Vanishing gradients in backprop.  
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/vanishing.png)  
Two simple approaches to help w vanishing gradients.
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/xavier.png)
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/kaiming.png)
Inception module was the first time I read paper about NN structures. and varies of inceptions were invented
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/googlenet.png) 
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/inceptions.png)
Normalization in 2015.  
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/norm.png) 
Batch Norm
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/batchnorm.png)
Norm in different dimentions
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/norms.png)
Resnet in 2015
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/resnet.png)
Can train much deeper nets by fitting the residual
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/smallchange.png)
All the key components of DL
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/checklist.png)
RNN is weight sharing in time
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/rnn.png)
It can do deep too  
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/deeprnn.png)  
and Google Translate in 2016 is an example.
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/googletranslate.png)
Use CNN for sequence model, which is **casual**
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/cnn.png)
WaveNet in 2016, used dilation, which is skip connections. 
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/dilation.png)
Attention is combining both RNN and CNN for sequency models
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/attention.png)
Transformers in 2017
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/transformer.png)
Applied in NLP, we have GPT
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/gpt.png)
AlphaFold is applied to proteins
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/alphafold.png)
Applied to general CV/audio
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/vit.png)
Takeaways, representation and deep/how to go deep
![Alt text](/assets/images/2024/24-03-13-Kaiming-MIT_files/takeaways.png)