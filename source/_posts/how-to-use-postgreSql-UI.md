---
title: how-to-use-postgreSql-UI
date: 2022-04-01 18:34:14
tags:
---
# 一个PostgreSQL 命令行UI
- [小日子过得不错写的](https://github.com/TaKO8Ki/gobang)
## 安装
```sh
 HOMEBREW_NO_AUTO_UPDATE=1 brew install tako8ki/tap/gobang
```
## 使用

- 基础帮助命令
  - gobang -h
  - gobang -v
  - gobang -c <config-path> 重新设置配置路径
- 使用方法
  - 默认连接数据库的配置路径在
  - `$HOME` 就是 `user/<your_name>`
  - macOS: `$HOME/.config/gobang/config.toml`
  - Linux: `$HOME/.config/gobang/config.toml`
  - Windows: `%APPDATA%/gobang/config.toml`
- 配置文件： 添加连接
  - 在`$HOME/.config/gobang/` 下面 `touch config.toml`
  - 编辑该文件。添加链接配置
- 链接并使用
## Keymap

| Key | Description |
| ---- | ---- |
| <kbd>h</kbd>, <kbd>j</kbd>, <kbd>k</kbd>, <kbd>l</kbd> | Scroll left/down/up/right |
| <kbd>Ctrl</kbd> + <kbd>u</kbd>, <kbd>Ctrl</kbd> + <kbd>d</kbd> | Scroll up/down multiple lines |
| <kbd>g</kbd> , <kbd>G</kbd> | Scroll to top/bottom |
| <kbd>H</kbd>, <kbd>J</kbd>, <kbd>K</kbd>, <kbd>L</kbd> | Extend selection by one cell left/down/up/right |
| <kbd>y</kbd> | Copy a cell value |
| <kbd>←</kbd>, <kbd>→</kbd> | Move focus to left/right |
| <kbd>c</kbd> | Move focus to connections |
| <kbd>/</kbd> | Filter |
| <kbd>?</kbd> | Help |
| <kbd>1</kbd>, <kbd>2</kbd>, <kbd>3</kbd>, <kbd>4</kbd>, <kbd>5</kbd> | Switch to records/columns/constraints/foreign keys/indexes tab |


# 另外的UI控制软件 Web 控制

[另外一个开源软件](https://github.com/prostgles/ui)
## 安装 下载

- By default the app will be accessible at localhost:3004. To use a custom port (3099 for example):
- `默认的短裤是3004 可以通过设置修改端口`
```
git clone https://github.com/prostgles/ui.git
cd ui
docker-compose up 


git clone https://github.com/prostgles/ui.git
cd ui
PRGL_DOCKER_PORT=3099 docker-compose up 
```

- 打开 http://localhost:3004
- 在 SQLEditor 进行编辑 SQL语句
- 在左侧进行查询表格

# 如何让它常驻
```
docker-compose pull
docker-compose up -d
```
