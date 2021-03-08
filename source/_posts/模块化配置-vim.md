---
title: 模块化配置 Vim
tags:
  - Neovim
  - Vim
id: '41'
categories:
  - - 好物推荐
date: 2019-01-10 10:20:25
---

![](https://cdn.pixabay.com/photo/2016/11/19/14/00/code-1839406__480.jpg) 之前就想过模块化vim配置, 但是没有合适的教程, 一直拖着, 直到看到此文章[Vimrc Init.vim太长了？不存在的](https://zhuanlan.zhihu.com/p/54864654), 根据Pendragon大佬的vim配置, 模仿并修改了一波.
<!-- more -->
## [dein.vim](https://github.com/Shougo/dein.vim)

> Dein.vim is a dark powered Vim/Neovim plugin manager.

我们先安装dein. 执行 `curl https://raw.githubusercontent.com/Shougo/dein.vim/master/bin/installer.sh > installer.sh` 然后 `sh ./installer.sh ~/.cache/dein` 好像有些同学喜欢把dein安装到`.config`下, 本人不是很喜欢, 所以还是按照文档示例中的路径安装. 安装成功后会提示如下信息, 之后我们会用到, 先保存起来:

```vim
"dein Scripts-----------------------------
if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=/root/.cache/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state('/root/.cache/dein')
  call dein#begin('/root/.cache/dein')

  " Let dein manage dein
  " Required:
  call dein#add('/root/.cache/dein/repos/github.com/Shougo/dein.vim')

  " Add or remove your plugins here like this:
  "call dein#add('Shougo/neosnippet.vim')
  "call dein#add('Shougo/neosnippet-snippets')

  " Required:
  call dein#end()
  call dein#save_state()
endif

" Required:
filetype plugin indent on
syntax enable

" If you want to install not installed plugins on startup.
"if dein#check_install()
"  call dein#install()
"endif

"End dein Scripts-------------------------
```

## 结构

具体结构类似这样: . ├── README.md ├── container │   ├── colors │   ├── config │   ├── deincfg.vim │   ├── ftplugin │   ├── mapping.vim │   ├── normal.vim │   └── plugin └── init.vim 接下来分别介绍每个文件和文件夹的作用, 举例一些简单的使用.

## init.vim

完全使用Pendragon的脚本, 只是根据自己的文件夹进行了路径修改.

```vim
for file in split(glob("/root/.config/nvim/container/*.vim"), '\n')
    exe 'source' file
endfor
```

应该很好理解, 遍历执行container路径下所有后缀为vim的文件.

## container

用来存放所有配置.

### deincfg.vim

加载dein插件, 需要注意以下几点:

*   添加插件的时候不使用`add`方法, 而是使用`load_toml`.
*   dein的路径一定要根据自身安装情况修改.
*   如果想在启动vim的时候, 检测有未安装插件并安装的话, 记得解注:
    
    ```vim
    if dein#check_install()
      call dein#install()
    endif
    ```
    

### mapping.vim

用于存放快捷键. 例如 `map <F2> :NERDTreeToggle<CR>` 来开/关目录树.

### normal.vim

根据情况放一些常用配置. 例如 `syntax on` 开启语法高亮.

## colors

用来存放自定义主题配色.

## ftplugin

用来放一些按需加载的配置, 例如我们可以根据文件类型, 使用不同的配置.

## plugin

安装插件以后需要一些自定义配置, 可以将这些自定义配置以插件名称命名, 写到对应的vim文件下

## config

保存`toml`文件, 配合deincfg.vim, 在dein插件加载的时候导入toml里的插件. 例如

```toml
[[plugins]]
repo = 'davidhalter/jedi-vim'
```

如果你解注了上述的三行配置, 在启动vim的时候会自动检测插件的安装情况, 进行操作. 当然你要绑定`plugin`下的自定义配置, 可以在插件下紧跟着添加: `hook_add = '/root/.config/nvim/container/plugin/jedi-vim.vim'` 来使自定义配置生效. _私信了Pendragon, hook\_add添加map映射, hook\_source添加call的插件方法_ 具体可以参考 > [\[dein.vim\] hook の便利な使い方](https://qiita.com/delphinus/items/cd221a450fd23506e81a) 主要是:

钩子的名字

当它被执行时

hook\_add

当dein.vim添加插件时

hook\_source

就在加载插件之前

hook\_post\_source

插件加载后立即

hook\_post\_update

插件更新后立即生效

hook\_done\_update

更新所有插件后立即

## 完事

模块化配置便于日后的维护和管理, 不同插件的自定义配置不会混淆或显得不整齐. 基本这些就是我个人模块化的配置, 有很多不完善的地方. github地址: [https://github.com/alpha87/nvim](https://github.com/alpha87/nvim) 可以参考一下我的配置. 目前我还没有用到延迟加载, 如果想延迟加载插件或一些配置, 可参考[模块化你的vim配置文件](https://zhuanlan.zhihu.com/p/55455386).