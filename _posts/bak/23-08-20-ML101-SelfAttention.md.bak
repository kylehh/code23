---
title: ML101 - Self Attention
mathjax: true
toc: true
categories:
  - Study
tags:
  - ML
---

One more good resource for this introduction is [here](https://cameronrwolfe.substack.com/p/decoder-only-transformers-the-workhorse)


What the output could be for a vector input sent to a model. "Sequence labeling" is #input=#output. 
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/sequencelabeling.png)

FC can consider a limited length context.
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/fc.png)

Self-attention is the solution to use full input as context.
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/selfattention.png)

The self-attention can be used directly on input or on middle layers. and can be processed in **parallel**!!!
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/inputorlayer.png)

First get Query and Key matrix
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/qk.png)

Get **Attention scores** by multiply Q and V
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/attentionscore.png)

Adding Softwax(Can replaced by ReLU.), and normalization. A masked version could be used here to achieve causality. 
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/softmax.png)
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/masked.png) 

Use attention scores as weights, and the output the weighted sum on V matrix
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/v.png)

Matrix view of the steps above.Q/K/V matrix are the paramters to be learnt. 
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/matrix.png)

Single head limits the ability of self-attention to focus on multiple positions within the sequence—the probability distribution can easily be dominated by one (or a few) words.
Now let's expand it to multi-head attention. Use the previous results as $b^{i,1}$
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/multihead.png)

And use another set of matrix to get $b^{i,2}$
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/multihead2.png)

Concatentate multi head results and times a matrix to get the final output $b^i$. Because each attention head outputs token vectors of dimension $d // H$, the concatenated output of all attention heads has dimension $d$
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/concat.png)

Adding position vector created by positial encoding. $sin$ encoding is hand crafted, and can be learnt as well.   
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/position.png) 

For speech, the vector seq. could be VERY long, 1 second signal is 100 vectors, and the complexity is square to the length. 
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/speech.png) 

Image can be seens as a long vector sequence as well.
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/image.png) 

CNN is simplified SA(self-attention) with limited receptive field.   
SA is CNN with **leanable** receptive field. 
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/cnn.png) 

SA needs more data to train than CNN. (Results from ViT paper, *An image is worith 16x16 word*)
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/compare.png) 

RNN can NOT be parallel processed and easy to forgot early input.
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/rnn.png) 

Use SA on graph, only consider the connected **edge**. and this is one type of **GNN**
![Alt text](/assets/images/2023/23-08-20-ML101-SelfAttention_files/gnn.png) 


