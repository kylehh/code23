---
title: FireAct and LLM Datasets
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

There are multiple LLM related datasets and I didn't really pay attentions to till I started working on [FireAct](https://fireact-agent.github.io/) demo.

1. FireAct  
It is short for **Fi**netune and **reAct**. It's paper from the reAct author, talking about how to finetune LLM specially for agents. 

This would be very helpful to help Llama model works with agents. So far my tests with LangChain's agent never worked with Llama mainly due to the quality of the output, not even mention the missing **function** feature. 

The paper's idea is to use GPT-4 to create QA trajectories, based on CoT, ReAct, or Relfexion, and format them as training datasets.   
![Alt text](/code23/assets/images/2023/23-10-15-LLM-FireAct_files/fireact.png)  

There are multiple LLM crowdsource datasets are used in this paper, and here they are:   

2. LLM DS: HotPotQA and BeerQA
This is a dataset for **multi-hop** qustion answering, which can be answered by ReAct.
Example here:  
Q: Ralf D. Bode is best known for his work on a film starring who as Loretta?  
A: Sissy Spacek  

A newer version is BeerQA, which is constructed based on SQuAD and HotPotQA.

3. LLM DS: SQuAD
Stanford Question Answering Dataset (SQuAD) is a reading comprehension dataset. The 2.0 version combines 100K questions from v1.1 and 50K unanswerable questions.   
So it provides a short passage and ask quetions based on it. 

4. LLM DS: StrategyQA  
StrategyQA is a question-answering benchmark where the required reasoning steps are implicit in the question and should be inferred using a strategy.   
Q: Are chinchillas cold-blooded?  
A: No  
Explanation: Chinchillas are rodents, which are mammals. All mammals are warm-blooded.  

5. TriviaQA
This is a reading comprehension dataset containing over 650K question-answer-evidence triples.   
Q: Who was the man behind The Chipmunks? 
A: David Seville
SearchResults: [{"DisplayUrl":"www.youtube.com/xxxx",...}]  

6. LLM DS: Bamboogle  
It's made up only of questions that Google answers incorrectly.  
Q: Who was president of the United States in the year that Citibank was founded?
A: James Madison  
7. LLM DS: MMLU  
Massive Multitasak Language Understanding. They are multiple choice questions.
Q: How many numbers are in the list 25, 26, ..., 100?  
(A) 75 (B) 76 (C) 22 (D) 23  
A: B  



