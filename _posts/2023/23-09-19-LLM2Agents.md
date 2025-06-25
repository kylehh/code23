---
title: From LLM to Agents
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Yao Shunyu, the original author of ReAct paper, talk about LLM and Agents.
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/overview.png)

## 1, ReAct
Reasoning Only: like CoT  
Acting Only: like WebGPT (search only)  
Need to combine both achive human-like results
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/react_overview.png)

Comparing action space with RL, RL is external only but ReAct is acting with both internal and external.  
How can we achieve long-term mem? check next point!
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/react_summary.png)



## 2 Reflexion
Noah Shinn, a undergraduate from northeastern is the first author of this paper. 

- Use language feedback instead of scalar feedback,0/1. A feedback would be like runtime error msgs, unit test cases and results. 
- Use language update, instead of parameter update like PPO, A3C, DQN.... An update would be like "be sure to handel this corner case"

So this "Verbal" RL would be called Reflexion.
How can we achieve long-term mem? check next point!
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/reflex_verbalRL.png)

Here is the summary of Reflexion.  
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/reflex_summary.png)

## 3 Tree of Thoughts
Shunyu's paper again. Why is it hard for computer to solve 24?  
1, Once you get it wrong at one step, it's impossible to get the results.   
2, How to evaluate early choices. Like if you start with number 5, is it good or bad?
So the planning is very important for soving this problem.  
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_overview.png)
Bandit of outputs: output all steps to generate 24.  
Tree of tokens: list all possible tokens.  
Thought: tradeoff between these two
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_thoughts.png)
Generate thought is the key, either by sampling or proposal. 
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_generationA.png)
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_generationB.png)
Evaluation
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_evaluationA.png)
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_evaluationB.png)
Search can be done by BFS or DFS

![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_summary.png)

Here is a simple promping solution with ToT, and here is the prompt
```
Imagine three different experts are answering this question.
All experts will write down 1 step of their thinking,
then share it with the group.
Then all experts will go on to the next step, etc.
If any expert realises they're wrong at any point then they leave.
The question is...
```  
![Alt text](/code23/assets/images/2023/23-09-19-LLM2Agents_files/tot_prompt.png)

