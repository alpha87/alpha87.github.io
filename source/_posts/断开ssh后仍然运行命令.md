---
title: 断开SSH后仍然运行命令
tags:
  - SSH
  - 终端
id: '28'
categories:
  - - Linux
date: 2018-02-15 09:26:13
---

![By Marlon Dutra](https://i.loli.net/2020/02/06/CQcnAlDih7uz4qZ.jpg) 因为经常要在云服务器上跑一些程序，但是一旦断开ssh连接就意味着程序的结束。这里提供两个简单的办法，即使断开连接也能继续运行。 我们来简单的了解一下。
<!-- more -->
## 使用nohup命令

命令格式（常用） `nohup Command` Command是你要运行的命令，比如说你要运行一个python文件，可以这样输入： `nohup python test.py` 其实就是在命令前加nohup，很简单吧？ 在使用nohup命令后会生成一个nohup.out文件,用于存放命令执行的结果。 一般情况下基本的命令都可以应对了。

## 使用screen命令

首先要安装screen： `pip install screen` 安装好之后输入screen就进入了screen会话，在screen会话中运行命令，断开ssh后也不会结束程序。 当然还有很多解决断开ssh连接后继续运行命令的办法，我选择了两个比较简单的，如果这两个命令还不能解决你的问题，那就请继续Google啦！