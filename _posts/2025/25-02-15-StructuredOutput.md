---
title: Structured Output
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

How can LLM follow the format defined in structured output?

One good explanation is this youtube [video](https://www.youtube.com/watch?v=xpvFinvqRCA) and [blog](https://blog.vllm.ai/2025/01/14/struct-decode-intro.html) from vLLM

## 0 A brief historical context
GOFAI(Good-Old-Fashioned AI) are deterministic and rule-based, given its intentionality is injected through explicit programming
NFAI(New Fangled AI) are often considered as “black-box” models, data-driven given the networked complexity nature of its internal representations

## 1 OpenAI API and Outlines lib 
OAI uses Pydantic and Outlier use Regex expression. 


A finte state machine was maintained for regular express output. You track at which state the tokens are in, and check if the following tokens are valid or not. 
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/fsm.png)

The performance of this method is hard to scale in real case. The solution is pre-generate all possibile tokens from this step
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/reg.png)

## 2 Context-Free Grammar
CFG(Context Free Grammar) allows multiple levels of nesting. Structured JSON with limited nesting can be solved by Regex but deeply nested structured text would need CFG.
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/cfg.png)
## 3 Pushdown Automata
PDA is a common approach to execute a CFG, which is a combination of FSM and a stack. There is a whole new field for me to understand automata, and I will skip the details...
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/pushdown.png)

[XGrammar](https://blog.mlc.ai/2024/11/22/achieving-efficient-flexible-portable-structured-generation-with-xgrammar) is an implementation of PDA and achieve great performance. You can think of a PDA as a **collection of FSMs, and each FSM represents a context-free grammar (CFG)**

There is **token-terminal mismatch problem** due to tokenization of LLM, and it would mess up the nesting matches.

## 4 Latest studies
One research about subgrammar
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/subgrammar.png)
A paper from Deepmind combined PDA(Pushdown Automata) and FSM.
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/pdafsm.png)

## 5 Performance
Structured output may hurt performance
![Alt text](/assets/images/2025/25-02-15-StructuredOutput_files/perf.png)

