---
title: Distributed System comparison
mathjax: true
toc: true
categories:
  - Study
tags:
  - Cloud
---
Again, I summarized the comparison between each distributed systems here. Couple of interesting points to **DASK**  

# Hadoop
runs at seconds in disk, SLOW
# Spark
10~100 ms  
Barrel Mode  
Gang scheduling  
DL SGD is not good fit into MapReduce

# DASK
micro-seconds  
peaked in 2021  
declined (data doesn NOT show, dependency  for other systems)  

BDFL Benevolent Dictator For Life  
# Ray
- Rely on upstream SQL and ML preprocessing support  
- Less support for deploymet   

# pyArrow
data storage memery format  
