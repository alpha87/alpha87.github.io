---
title: Scrapy学习笔记（四）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '114'
categories:
  - - 编程学习
date: 2017-07-08 12:17:16
---

学习scrapy中spider的用法，主要介绍一些方法的使用。
<!-- more -->
# name

每个spider都有自己的名字，这个名字必须唯一，这样我们才能指定运行我们的spider。命名没什么要求，不过尽力要直接体现spider，比如爬取taobao.com，那我们就取名taobao。

# start\_urls

爬虫起始列表。这里保存的都是待爬取的URL。

# allowed\_domains

用来定义允许爬虫爬取的取名。例如在爬取淘宝过程中爬取到其他域名非taobao.com，那么就跳过。这个选项是可选的。

# custom\_settings

先声明这个为字典格式。spider中有一些特殊的配置信息可以使用这个属性，设置以后在运行spider会直接使用custom\_settings配置的信息，跳过检查配置文件。

# from\_crawler()

我们利用这个方法获取爬虫的配置信息，例如在上一节讲到的：

```python
@classmethod
def from_crawler(cls, crawler):
    return cls(
        mongo_uri=crawler.settings.get('MONGO_URI'),
        mongo_db=crawler.settings.get('MONGO_DB')
        )
```

这个在获取配置信息的时候经常用到。

# start\_requests()

这个方法必须返回一个可迭代对象。该对象包含了spider用于爬取的第一个Request。我们可以在参数中定义URL，method，callback等信息。

# make\_requests\_from\_url(url)

该方法接受一个URL并返回用于爬取的Request对象。该方法在初始化request时被start\_requests()调用,也被用于转化url为request。

# logger

用来输出日志，例如输出访问网页的状态码： `logger.info(response.status)` 在输出日志中会体现URL的状态码。

# parse

默认回调函数，如果我们在使用过程中没有特殊规定，都是使用这个方法来解析网页。

# closed

当spider关闭时,该函数被调用。例如我们在结束爬虫时要断开与数据库的链接，我们就可以使用这个方法。也可以在运行结束后提示我们已经运行完成。 这些就是spider中常用的方法和属性，读者们可以按需使用，详细内容可以阅读官方文档——[spider用法](https://doc.scrapy.org/en/latest/topics/spiders.html)。