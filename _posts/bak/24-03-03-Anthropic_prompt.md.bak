---
title: Prompt Engineering for Anthropic
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Varies of prompt engineering tricks for Anthropic Claud

## 1 Format
```
\n\nHuman: user prompt here.  
\n\nAssistant:
```
## 2 Be clear and direct
```
Human: Who is the best basketball player of all time? **Please choose one specific player.**
```
Assistant:
## 3 Role assignment (Role prompting)
```
Human: **You are a highly experienced climate scientist.** Please explain the impact of greenhouse gasses.  
```
Assistant:  
## 4 Seperate data from instrcutions. With \{\{\}\} and XML tags  
```
Human: Hey Claude. <email>{{EMAIL}}<\email> Make this email more polite.

Assistant
```
## 5 Output Formatting
```
Human: Please write a haiku about {{ANIMAL}}. **Put it in <haiku> tags.**

Assistant: **<haiku>**(This is optional)  
```
Another example of **Speaking for Claude** is to supply part of the answer as you wish:
```
Human: Who is the best basketball player of all time? Please choose one specific player.

Assistant: Stephen Curry is the best player. We have 3 reasons, 1st being	
```
## 6 Precognition (Thinking Step by Step) CoT
```
Human: Is this review sentiment positive or negative? First write the best arguments for each side in <positive-arg> and <negative-arg> XML tags, then answer.

This movie blew my mind with its freshness and originality. Unrelatedly, I have been living under a rock since 1900.

Assistant:
(Claud actually prefer to the second opinion. If we swtich to <negative-arg> and <positive-arg>, the answer would be totally opposite)
```
Another examples:  
```
Human: Use the following clues to answer the following multiple-choice question, using the following procedure:  
(1) First, go through the clues one by one and consider whether the clue is potentially relevant  
(2) Second, combine the relevant clues to reason out the answer to the question  
(3) Third, map the answer to one of the multiple choice answers: either (a), (b), or (c)
``` 
## 7 Few Shot Prompting
```
Human: Please complete the conversation by writing the next line, speaking as "A".
Q: Is the tooth fairy real?
A: Of course, sweetie. Wrap up your tooth and put it under your pillow tonight. There might be something waiting for you in the morning.
Q: Will Santa bring me presents on Christmas?

Assistant:
```
## 8 Avoiding Hallucinations
First, Give Claude a way out to say I don't know
```
Human: Who is the heaviest hippo ever recorded? **Only answer if you know the answer with certainty.**
```
Another way is to find evidence before answering. Using **scratchpad** as in LangChai
```
Human: <question>What was Matterport's subscriber base on the precise date of May 31, 2020?</question>
Please read the below document. Then, in <scratchpad> tags, pull the most relevant quote from the document and consider whether it answers the user's question or whether it lacks sufficient detail. Then write a brief numerical answer in <answer> tags.
```

## 9.1 Chain Prompt
```
{{CONVO_HISTORY}}

Human: Please check over your answer, making sure that it is accurate, and fixing it if you see any mistakes.
```

## 9.2 Function Calling
In function calling, **we substitute function results into prompts**. Claude can't literally call or access functions. Instead, we have Claude **output the function name and arguments, halt while the function is called, then we reprompt with the function results.**
```
<tools>
  <tool_description>
    <tool_name>calculator</tool_name>
    <description>
       Calculator function for doing basic arithmetic. Supports addition, subtraction, multiplication
    </description>
    <parameters>
      <parameter>
        <name>first_operand</name>
        <type>int</type>
        <description>First operand (before the operator)</description>
      </parameter>
      <parameter>
        <name>second_operand</name>
        <type>int</type>
        <description>Second operand (after the operator)</description>
      </parameter>
      <parameter>
        <name>operator</name>
        <type>str</type>
        <description>The operation to perform. Must be either +, -, *, or /</description>
      </parameter>
    </parameters>
  </tool_description>
</tools>
```
