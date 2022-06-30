---
title: 设置prettier和eslint
date: 2022-05-12 18:05:57
tags:
---

## 是什么

- prettier 是一款代码格式化工具， eslint 是一款代码质量检查工具（但是也可以检查格式）认清楚这两个点后续的理解就比较轻松了

## prettier

- 配合编辑器使用，在保存的时候自动格式化代码 对于 vscode 来说
  - 打开编辑器自带的保存自动格式化
  - 安装 prettier 插件。启用插件。 插件设置 prettier.enable true
- 通过 `.prettierrc`自定义格式化逻辑
  - 常用配置
  ```
      {
        "printWidth": 100, // 默认是80
        "singleQuote": true,
        "trailingComma": "all",
        "useTabs": false,
        "tabWidth": 2,
        "bracketSpacing": true,
        "arrowParens": "always"
      }
  ```
- 通过 `.prettierignore` 设置不需要 format 的文件

## eslint

- 通过不同使用已有的规则集，约束代码质量

- 可以通过不同 插件。引入使用不同规则集。
