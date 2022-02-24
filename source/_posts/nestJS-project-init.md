---
title: nestJS-project-init
date: 2022-02-24 19:28:57
tags:
---
## 如何初始化 nestJS 项目

## 语言篇
- typescript
  - 
- ESlint
- .gitignore
- gitHook
- init app module

## 工具
- 设置 ESlint, prettier 扩展
- 使用 nest-cli 
  - [使用费命令](https://github.com/nestjs/nest-cli/blob/master/commands/new.command.ts)
  - nest new . 在当前目录生成。 `注意会跟README.md 有合并冲突，需要将其先删掉`
  - nest new {project-name} --directory `[位置路径]`
  - nest new {project-name} --skip-git 
