---
title: 使用 virtualenv 和 virtualenvwrapper 搭建 Python 虚拟环境
tags:
  - Python
  - 虚拟环境
id: '64'
categories:
  - - 编程学习
date: 2018-12-15 11:08:36
---

![](https://cdn.pixabay.com/photo/2014/05/27/23/32/matrix-356024__480.jpg) 使用python语言编程的过程中，难免会在系统里安装多个python版本。可能根据开发项目的需要，同一库会使用到不同版本，不同库之间版本不一致出现冲突等情况。这时候可以使用virtualenv管理虚拟环境。
<!-- more -->
## 安装

安装virtualenv和virtualenvwrapper，两者配合会更方便我们使用python虚拟环境。 `pip install virtualenv virtualenvwrapper` 安装好以后需要简单的配置。

## 配置

在你的shell配置文件中加入以下两行：

```shell
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

例如我使用的是zsh，那么在`~/.zshrc`文件中加入这两行。 更新配置文件： `source ~/.zshrc`

## 使用

简单罗列一些常用的命令，如果需要查看全部命令或了解一些命令可接的参数，请查看[virtualenvwrapper Doc](https://virtualenvwrapper.readthedocs.io/en/latest/index.html) **创建虚拟环境** `mkvirtualenv pyvenv` 在WORKON\_HOME中创建一个名为`pyvenv`的虚拟环境。 **显示虚拟环境的详细信息** `showvirtualenv pyvenv` **删除虚拟环境** `rmvirtualenv pyvenv` **列出或使用虚拟环境** 输入`workon`会打印可用的虚拟环境，在其后加上虚拟环境名称则进入该虚拟环境。 **离开虚拟环境** `deactivate`