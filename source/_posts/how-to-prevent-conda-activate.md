---
title: how-to-prevent-conda-activate
date: 2022-03-01 18:59:29
tags:
---


### 如何阻止conda 每次启动的时候自动激活base 环境

- 命令行设置
```sh
conda config --set auto_activate_base false
```
- 手动修改 ·.condarc ·
```text
# auto_activate_base (bool)
#   Automatically activate the base environment during shell
#   initialization. for `conda init`
auto_activate_base: false
```
- 设置环境变量  `CONDA_AUTO_ACTIVATE_BASE=false`
