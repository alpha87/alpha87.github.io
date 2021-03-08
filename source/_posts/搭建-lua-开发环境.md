---
title: 搭建 Lua 开发环境
tags:
  - Lua
id: '27'
categories:
  - - 编程学习
date: 2019-04-16 09:19:28
---

![Lua](https://i.loli.net/2020/02/06/wPgo6lWLnCHe5if.png) [Lua](https://www.lua.org/) 是一种强大、高效、轻量级、可嵌入的脚本语言。它支持过程编程、面向对象编程、函数编程、数据驱动编程和数据描述。
<!-- more -->
首先需要搭建Lua的开发环境。 我们安装Lua最新版本 5.3.5

## 安装

```
curl -R -O http://www.lua.org/ftp/lua-5.3.5.tar.gz
tar zxf lua-5.3.5.tar.gz
cd lua-5.3.5
make linux test
```

如果是MacOSX，最后一条命令需要使用`make macosx test`。 安装完成后创建软链：ln -s ~/lua-5.3.5/src/lua /usr/bin/lua

## 注意

在安装过程中如果报错为：`readline/readline.h: No such file or directory`，说明缺少依赖。 Ubuntu：`apt-get install -y libreadline-dev` CentOS：`yum install -y readline-devel`

## Hello World

安装完成后在终端输入：

```
~ lua
Lua 5.3.5  Copyright (C) 1994-2018 Lua.org, PUC-Rio
> print("hello, world")
hello, world
```

使用`Ctrl+C`退出交互式界面。 也可以创建文件 test.lua，内容同上。 终端执行：

```
~ lua test.lua 
hello,world
```

这样Lua的开发环境就安装完成了。