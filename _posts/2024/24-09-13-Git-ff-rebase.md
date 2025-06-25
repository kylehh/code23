---
title: Git Merges
mathjax: true
toc: true
categories:
  - Study
tags:
  - Git
---

## Merge vs Rebase
Great explanation from this [tutorial](https://www.atlassian.com/git/tutorials/merging-vs-rebasing) and this [video](https://www.youtube.com/watch?v=DkWDHzmMvyg&t)

## 1 Merge
`git merge main`is a **non-destructive** operation. The existing branches are not changed in any way, and *feature* branch will have an extraneous **merge commit** every time you need to incorporate upstream changes.   
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/merge.png)  

It's a good pratice to merge changes from main and eventually do a PR to when pushing the feature to main
`git push origin feature`
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/mergeoften.png)

## 2 Fast-Forward Merge
When there is **no new commit** in the main branch, mering a feature branch would lead to fast-forward merge.
`git merge feature`
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/ff1.png)  ![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/ff2.png)  

## 2 Rebase 
When there is new commit in main and it's forked situation. FF is not feasible and a rebase or 3 way merge is needed

`git rebase main` moves the entire *feature* branch to begin on the tip of the *main* branch, effectively incorporating all of the new commits in main. It **re-writes** the project history.  
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/rebase.png)  

Use `git rebase --continue` after solve the conflicts. and staging them after the changes. or `--abort` to quit

Use `git rebase --interactive HEAD~3` to interactively rebase previous 3 commits. 

Now you can fast-forwrd merge the feature branch in main.(or squash and merge, which **squash** multipl commits into one)
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/squash.png) 

## 3 The golden rule of rebasing
**Never** use it on public branch.
It will force *main* to get all commit from you, while others don't need. 
![Alt text](/code23/assets/images/2024/24-09-13-Git-ff-rebase_files/rebase_feature.png)

**Never** use it on already pushed commits.


