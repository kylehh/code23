---
title: Git Undo
mathjax: true
toc: true
categories:
  - Study
tags:
  - Git
---

I will start with simple [cheat sheet]([2f5451f](https://www.nobledesktop.com/learn/git/undo-changes)) before diving into reset/revert/checkout

- Undo changes
  `git checkout changed_file`
- Undo `git add`  
  `git checkout HEAD added_file`  # changes to the file is lost  
or `git reset (HEAD) added_file`    # changes persist, HEAD is default
- Undo `git commit`  
  `git reset --soft HEAD~`
- Undo `git push`  
  `git revert --no-edt 2f5451f`  
  followed by `git push`

There are 3 working trees: the Working Dictory, `git add` to the Staging Index, and `git commit` to the Commit Tree (HEAD)
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/three-trees.png)

## 1 Checkout
Checkout a branch is the most common usage and what happens behind the scence is moving **HEAD** to a specific **commit**
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/checkout.png)

Here is the example of `git checkout hotfix` followed by `git checkout HEAD~2`
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/checkout-head.png)  
## 2 Reset
A reset is an operation that takes a specified commit and resets the "three trees" to match the state of the repository at that specified commit.  
`git checkout hotfix` followed by `git reset HEAD~2` will leave **orphaned commits**
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/reset-head.png)

## 3 Revert
Reverting undoes a commit by creating a new commit. This is a **safe** way to undo changes, as it has no chance of re-writing the commit history. 
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/revert-head.png)
## 4 Comparison

|Work level |Reset | Revert | Checkout|  
|---|--|---|---|
|Commit|Discard commits in a private branch|Undo commits in a public branch|Switch between branches (or inspect old snapshots)|
|File|unstage a file|N/A|Discard changes in the working directory|

## 5 Reset vs Check a FILE
`git reset (HEAD) file_name`  
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/reset-file.png)
**The --soft, --mixed, and --hard flags do not have any effect on the file-level version of git reset**, as the staged snapshot is always updated, and the working directory is never updated. Thus **changes persist**

`git checkout HEAD file_name`  
Checking out a file is similar to using git reset with a file path, except it updates the working directory instead of the stage, so you **lost the changes**. Unlike the commit-level version of this command, this does not move the HEAD reference, which means that you won’t switch branches.

This is like `git reset HEAD --hard` but on one file
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/checkout-file.png)

## 6 Reset Options
![Alt text](/assets/images/2024/24-09-10-Git-undo_files/reset-options.png)

`--mixed` is the default option for `git reset commit_hash` and it undo `git add` options but not resetting working directory, so **changes persist**

`--hard` would reset working directory so changes are lost

`--soft` is resetting HEAD only. 