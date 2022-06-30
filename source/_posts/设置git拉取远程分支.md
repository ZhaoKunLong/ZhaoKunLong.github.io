---
title: 设置git拉取远程分支
date: 2022-06-08 18:58:06
tags:
---

# git branch -a
列出所有分支. 包括远程分支

# git branch -r 
列出所有远程分支

# git checkout -b  本地分支名称X origin/远程分支名称X
在本地创建分支并追踪远程分支

# git fetch 
将本地分支和远程分支数据同步

# git fetch -a
将本地所有分支和远程分支同步

# git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
将所有远程分支同步到本地

# git branch | grep "<pattern>"
通过模式匹配分支名称

# git branch | grep "<pattern>" | xargs git branch -D
删除匹配的 分支

