---
title: Avoid secret leak in GIT
mathjax: true
toc: true
categories:
  - Study
tags:
  - Git
---

Frankly speaking security is the area I care the least. Not interested in any security related topics except for RSA, which is only because the algorithm behind it is beautiful. 

But here are some common practices to avoid secret leak in GIT
## 1 Pre-Commit hook
Pre-Commit hook could check your changes before commiting
As long as you provide password pattern and proper detection code, it will deal with it

- Add to your `.pre-commit-config.yaml`
```python
- repo: https://github.com/tuttlebr/nv-pre-commit
  rev: v0.0.3 # Use the ref you want to point at
  hooks:
    - id: detect-nv-keys
```
and the source code for this hook is [here](https://github.com/tuttlebr/nv-pre-commit)

## 2 BFG
Even if you remove secrets in your main but it still leave in your git history. [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) is the tool to remove secrets in git history by directly modifying your git database, scrube it. 

1. Download the binary and rename it to `bfg.jar`  
`wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar`
2. Clone your repo by just mirror  
`git clone --mirror git://example.com/some-big-repo.git`
3. Run BFG against your repo. Here `password.txt` contains the secrets you want to remove  
`java -jar bfg.jar --replace-text passwords.txt  my-repo.git`
4. Prune your git database and push
```sh
cd some-big-repo.git
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push
```
