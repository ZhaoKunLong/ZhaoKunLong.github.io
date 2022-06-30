---
title: 如何revert一个Commit
date: 2022-05-20 19:10:35
tags:
---

## 如何撤回一个提交
- 例子：
  - git commit -m 'feat: XXXX'  提交后，会有一个hash
  - 如果是刚提交的，可以有两种办法将其撤回
    1. git reset --soft HEAD^ 这样的话，会撤回提交，但是会保存所作出的改动
    2. git rest --hard HEAD^ 这样的话会撤回提交但是会保存改动
  - 如果不是刚提交的是之前提交的
    1. git reset --soft `hash` 不丢弃改动，然后把那一次的提交撤回，这样做的话，需要当前的工作区是没有改动的，/干净的。 这样做比较容易有冲突。
    2. git reset --hard `hash` 将改动丢弃，然后把那一次的提交撤回

## 如果一个提交已经push 到了远程，怎么撤回
- 例子：
  - git commit -m 'feat: xxx'
  - git push
  - 拿到 提交的hash
  - 将上面的reset 改为revert. 这样做就是将提交丢弃，修改后再提交。将会把原来的提交revert掉。 那些改动奖会被丢弃
    1. git revert `hash`
    2. git push
  - 如果只是修改改动的话。
    1. git reset --soft `hash`
    2. git revert `hash`
    3. git stash
    4. git push     到这里就将改动留在本地，染远程将改动丢弃
    5. git stash pop 
    6. git commit -m 'fix: change XXX'  将改动，修改后，再重新提交。
    7. git push   推送到远程
