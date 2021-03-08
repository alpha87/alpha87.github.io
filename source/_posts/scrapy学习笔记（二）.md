---
title: Scrapy学习笔记（二）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '110'
categories:
  - - 编程学习
date: 2017-06-22 12:13:01
---

通过这篇文章，我们会了解到 scrapy 的命令行工具。

文章介绍命令行工具的顺序基本符合官方文档的顺序。

# 命令

scrapy的命令分为全局命令和项目内命令。全局命令也就是不需要在项目所在目录下运行，项目内命令必须在生成项目后，在此目录下运行的命令。举个栗子，`startproject`就是全局命令，因为在运行这个命令的时候还没有项目，`check`就是项目内命令，因为必须有项目才能检查代码正确与否。

## Global commands:

*   startproject
*   genspider
*   settings
*   runspider
*   shell
*   fetch
*   view
*   version

## Project-only commands:

*   crawl
*   check
*   list
*   edit
*   parse
*   bench

# 创建项目

命令：

```bash
scrapy startproject testproject
```

这个命令用于生成我们所需要的爬虫项目。进入到该目录中，会发现生成了许多文件。这些文件的用法在以后都会一一详解。

```bash
.
└── testproject
    ├── scrapy.cfg
    └── testproject
        ├── __init__.py
        ├── items.py
        ├── middlewares.py
        ├── pipelines.py
        ├── settings.py
        └── spiders
            └── __init__.py
```

# 生成 spider

命令：

```python
scrapy genspider baidu www.baidu.com
```

进入到 spider 文件夹下，输入该命令会生成一个名为 baidu.py 的文件，cat 这个文件，我们会发现其实就是最基本的 spider 模板。

```python
# -*- coding: utf-8 -*-
import scrapy

class BaiduSpider(scrapy.Spider):
    name = 'baidu'
    allowed_domains = ['www.baidu.com']
    start_urls = ['http://www.baidu.com/']

    def parse(self, response):
        pass
```

## 模板选择

在终端输入命令可以查看生成模板的类型：

```bash
scrapy genspider -l
```

输出：

```bash
Available templates:
  basic
  crawl
  csvfeed
  xmlfeed
```

也就是系统自带这四种模板，如果没有指定模板，会选择`basic`基本模板。如果你问我可以自定义模板吗，当然可以了，不过本文暂时不涉及这个问题，如果有需要可以先自行google。

使用模板命令：

```bash
scrapy genspider -t xmlfeed zhihu www.zhihu.com
```

`-t`就是TEMPLATE，也就是模板。

例子：

```bash
$ scrapy genspider -l
Available templates:
  basic
  crawl
  csvfeed
  xmlfeed

$ scrapy genspider example example.com
Created spider 'example' using template 'basic'

$ scrapy genspider -t crawl scrapyorg scrapy.org
Created spider 'scrapyorg' using template 'crawl'
```

# crawl

用于运行指定spider

命令：

```bash
scrapy crawl baidu
```

# check

`check`用来检查项目中的代码是否有错误。如果没错会返回ok，如果有错会定位错误代码的位置。

命令：

```bash
scrapy check
```

示例：

```bash
➜  testproject scrapy check

----------------------------------------------------------------------
Ran 0 contracts in 0.000s

OK
```

# list

运行命令会列出项目中所有的spider。

命令：

```bash
scrapy list
```

示例，在我们创建的项目目录下运行：

```bash
➜  testproject scrapy list
baidu
zhihu
```

# shell

运行这个命令会进入命令行交互模式，以给定的 URL(如果给出)或者空(没有给出 URL)启动 Scrapy shell。查看 Scrapy 终端(Scrapy shell)可以做一些简单的操作，可以使用选择器快速获取信息，方便调试。

# fetch

使用这个命令会执行一次请求，并调用scrapy的下载器，返回网页的源码。

命令：

```bash
scrapy fetch http://www.baidu.com
```

还可以加三个参数：

`--nolog` `--headers` `--no-redirect`

分别是不输出日志信息，返回网页的请求头和禁止重定向。如果网页没有重定向的话返回的还是原网页。

# view

命令：

```bash
scrapy view http://www.taobao.com
```

这个命令比较有用，它的作用是请求网址，输出网址的源码，并将该网页保存成一个文件，使用浏览器打开。如果打开的网址和你正常加载的网页有所不同，一般情况下没显示的部分使用了异步加载。因此该命令可以用来检查 spider 所获取到的页面,并确认这是您所期望的。

这样在你以后的抓取过程中就可以使用这个命令分析网页是否使用了异步加载。

# runspider

这个命令和crawl命令的区别在于crawl命令后是spider的`name`，而runspider命令后加的是爬虫的文件名，在本文的项目中，使用crawl命令：

```bash
scrapy crawl baidu
```

使用runspider就是：

```bash
scrapy runspider baidu.py
```

# settings

用来获取项目的配置信息。

例如获取项目名称：

```bash
➜  testproject scrapy settings --get BOT_NAME
testproject
```

# edit

如果你不使用vim作为编辑器的话，这个命令不常用，因为这个命令会调用vim来编辑文件。

命令：

```bash
scrapy edit baidu
```

# parse

获取给定的 URL 并使用相应的 spider 分析处理。如果您提供 –callback 选项,则使用 spider 的该方法处理,否则使用 parse

使用上一篇的例子：

```bash
scrapy parse http://quotes.toscrape.com -c parse
```

支持的操作：

\--spider = SPIDER: bypass spider autodetection and force use of specific spider 跳过自动检测 spider 并强制使用特定的 spider

\--a NAME = VALUE: set spider argument (may be repeated) 设置 spider 的参数(可能被重复)

\--callback or -c: spider method to use as callback for parsing the response spider 中用于解析返回(response)的回调函数

\--pipelines: process items through pipelines 在 pipeline 中处理 item

\--rules or -r: use CrawlSpider rules to discover the callback (i.e. spider method) to use for parsing the response 使用 CrawlSpider 规则来发现用来解析返回(response)的回调函数

\--noitems: don’t show scraped items 不显示爬取到的 item

\--nolinks: don’t show extracted links 不显示提取到的链接

\--nocolour: avoid using pygments to colorize the output 避免使用 pygments 对输出着色

\--depth or -d: depth level for which the requests should be followed recursively (default: 1) 指定跟进链接请求的层次数(默认:1)

\--verbose or -v: display information for each depth level 显示每个请求的详细信息

# bench

这个命令会运行 benchmark 测试，模拟测试scrapy的爬取速度。

# version

这个命令可以查询当前scrapy的版本，和一些依赖库版本信息。

示例：

```bash
➜  ~ scrapy version
Scrapy 1.8.0

➜  ~ scrapy version -v
Scrapy       : 1.8.0
lxml         : 4.5.0.0
libxml2      : 2.9.10
cssselect    : 1.1.0
parsel       : 1.5.2
w3lib        : 1.21.0
Twisted      : 19.10.0
Python       : 3.8.0 (v3.8.0:fa919fdf25, Oct 14 2019, 10:23:27) - [Clang 6.0 (clang-600.0.57)]
pyOpenSSL    : 19.1.0 (OpenSSL 1.1.1d  10 Sep 2019)
cryptography : 2.8
Platform     : macOS-10.14.6-x86_64-i386-64bit
```