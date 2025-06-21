---
title: AlphaGeometry
mathjax: true
toc: true
categories:
  - Study
tags:
  - ML
---

I have been sick since the trip to China and haven't fully recovered even till today. I totally underestimated the damage of 雾霾（smog, a word combining smoke and fog) and had a 3 miles run without mask. I deserved it...

First blog in the new year, a amazing results from Deep Mind team, AlphaGeometry, solves the IMO level Olympian math problems. Couple of take aways

1. It's for geometry problems only. Apparently geometry is one of the most easy categories to convert to symbolic formatting. I was very surprised to noticed that the previous state of art, **Wu's method**, which can solve 10 out of 30 quesitons, was publiced in [1978](https://www.maths.ed.ac.uk/~v1ranick/papers/wu.pdf). Great respect to Dr 吴文俊

2. Alegra problems will be tackled next I believe. Due to the synthetic data generation methods used in AlphaGeometry, I believe it can be applied to algebra as well. Number theories and combinatorics will be harder.

3. Fig 5 in this paper is clearly wrong. ABC should be a acute-angled triangle. I searched IMO problems and ABC was drawn correctly. Well, maybe there is no figure in original test sets, but the [solutions](https://web.evanchen.cc/exams/IMO-2004-notes.pdf) from Even Chen draw it correctly. 
![Alt text](/assets/images/2024/24-01-19-AlphaGeometry_files/nature.png)
![Alt text](/assets/images/2024/24-01-19-AlphaGeometry_files/evenchen.png)

4. So the AlphaGeometry found out that O is middle point of BC is unnecessary premise, is the acute angle premise also unnecessary? That's sth beyond my understanding now, need to dig into the proof to find out. 

