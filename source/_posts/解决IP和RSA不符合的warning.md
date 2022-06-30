---
title: 解决IP和RSA不符合的warning
date: 2022-06-30 18:34:35
tags:
---

P 地址和 RSA 主机密钥不同错误
SSH 将允许您从任何地方登录到您的远程服务器/计算机，它是非常重要的工具。

修复从 ubuntu 到 mac 的无密码输入

您可以通过两种主要方式登录服务器：

使用用户名和密码
使用 ssh 密钥使用无密码方法
如何使用 ssh

当您使用 ssh without password 时，您可能会收到警告The RSA host key different from the key for the IP Address 警告

RSA 主机密钥与 IP 地址的密钥不同，当您尝试 ssh 到服务器时会出现警告。

当远程服务器更改或 ip 匹配不再有效时，这是 known_hosts 的典型错误。

解决方案
在客户端做

cd ~/.ssh
>known_hosts

以上将清除 known_hosts 文件。但这会清除已知主机中的所有内容。

`如果您知道违规行，则只能清除`
