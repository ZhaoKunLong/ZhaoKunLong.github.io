---
title: how-to-download-file-from-remote-sever
date: 2022-02-23 19:11:30
tags:
---

###  本地和服务器 如何进行文件下载上传

1. 使用vscode remote 连接上远程服务器
  - 然后本地上传 就可以使用拖拽复制的方法
  - 从远程下载到本地就可以通过右键有个下载。

2. 使用命令行
- [推荐2个命令快速在本地和服务器之间上传下载文件](https://cloud.tencent.com/developer/article/1842814)
- lrzsz
- Ubuntu用户使用命令 sudo apt-get install lrzsz安装。
- Centos用户使用命令 sudo yum install -y lrzsz安装。
- `sz下载文件`
- 在服务器终端运行sz file将选定的服务器文件下载到本地；如果本地有重名文件，新下载的文件会自动重命名。 不适用mac, mac下需要搭配其他客户端来打开文件夹
- rz 上传文件 `在本地运行 rz`

3. scp  `在本地使用 scp`
- 上传本地文件到服务器：`scp /本地文件路径/test.txt root@123.123.123.123:/服务器文件路径/`
- 下载服务器文件到本地：`scp root@123.123.123.123:/服务器文件路径/test.txt /本地路径/`