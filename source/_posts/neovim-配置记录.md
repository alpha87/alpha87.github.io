---
title: Neovim 配置记录
tags:
  - Neovim
  - Vim
id: '104'
categories:
  - - 好物推荐
date: 2019-01-20 12:06:47
---

![](https://cdn.pixabay.com/photo/2016/03/09/09/17/computer-1245714__480.jpg) 第一次接触vim还是在玩树莓派的时候，当时打开 vim 想要退出界面是真的难，只能强行关闭终端。 pycharm 用来写 python 很顺手，但是功能太多也显得臃肿。 pycharm 的强大莫过于异常自动补全，跳转，代码调试功能，其他功能也很好，但是基本很少用到。 所以也是为了学习，还是勉强换成了 vim。用起来也有很多的不顺手。只能慢慢适应。
<!-- more -->
怕折腾还是使用 pycharm 吧，虽然有 ideaVim 插件，但是我用起来总觉得怪怪的。 不过用一段时间 vim 后，有些操作确实很有效率，快捷键虽多，平时敲到的也就十几个常用的，用的多了自然就熟练了。 **以下vim都指的是nvim**

# 配置地址

https://github.com/alpha87/vim-config 支持伪一键安装。说白了就是把 `init.vim` 文件复制到 `~/.config/nvim` 下。 安装插件还是需要进入nvim执行 `:PlugInstall` 命令，本来也能写到shell脚本里，但是会造成卡死，影响使用效果，还是需要用户自己执行命令，顺便可以看一下插件都用到了哪些第三方库。 不是很懂spaceVim是如何做到第一次进入自动安装的操作。

# 使用提示

## 相关python包

用 vim 主要是用来写 python，少不了格式化和代码校验，所以还需要安装 autopep8 和 flaske8。 `pip install --user autopep8 flake8` 即可。

## 插件配置

自动补全使用 YCM，需要安装 cmake 支持。 Ubuntu 下 `apt-get install -y cmake` 安装。 安装完成后在终端输入 `/Applications/CMake.app/Contents/MacOS/CMake --install`。 Mac可以在 https://cmake.org/install/ 下安装。 安装好后到 `~/.config/nvim/plugged/YouCompleteMe` 下执行 `./install.py` 等待安装完成。 文件中包含了映射好的快捷键，记住使用起来很方便。 虽然这份配置是我一点一点积累敲出来了，但我也记不清键具体是对应什么操作，想不起来的时候也只能打开文件看一看。 **相信用多了就会形成肌肉记忆吧。 :)**