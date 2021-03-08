---
title: Scrapy学习笔记（一）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '116'
categories:
  - - 编程学习
date: 2017-06-20 12:18:42
---

作为 scrapy 学习笔记的第一篇，主要是了解此框架的基本使用。并通过一个 demo 感受 scrapy 框架的强大。
<!-- more -->
# scrapy

来自[Scrapy](https://scrapy.org/)[官网](https://scrapy.org/)的介绍：

> Scrapy is a fast high-level web crawling and web scraping framework, used to crawl websites and extract structured data from their pages. It can be used for a wide range of purposes, from data mining to monitoring and automated testing.

我们暂时就认为它是一个功能加大的爬虫框架即可。

# Quotes to Scrape

我们要练习抓取的是 scrapy 官方提供的网站：[quotes.toscrape.com](http://quotes.toscrape.com/)。对这个网站的抓取让我们对 scrapy 框架有个基本的认识，可以更轻松的入门。因为在我最初接触框架的时候，总觉得它是一个很神秘很复杂的东西，还不如使用 requests 库更容易。 这个网站主要是一些名人名言，虽然看似简陋，却包含了文本，标签，超链接等大多数网站都具备的格式。所以这个网站用来入门 scrapy 是不二选择啊！

# Demo

## 抓取流程

我们通过抓取第一页的信息，获取该页的内容和下一页的链接，实现翻页抓取，然后将抓取到的网页内容保存为特定的格式并存入数据库。

## 创建项目

在命令行输入

```
scrapy startproject quotes
```

然后输入命令创建spider

```
cd quotes scrapy genspider quote quotes.toscrape.com
```

创建好spider文件后,我们就可以继续完善代码了。

## 初步测试

我们先来测试下框架，在生成好的spider文件中，我们先抓取网页的状态码和网页源码。 代码为：

```
# -*- coding: utf-8 -*- import scrapy  class QuoteSpider(scrapy.Spider):     name = "quote"     allowed_domains = ["quotes.toscrape.com"]     start_urls = ['http://quotes.toscrape.com/']      def parse(self, response):         print(response.status)         print(response.text)
```

这是运行的部分截图，正常输出了网页的状态码和网页源码。 接下来我们就开始正式抓取了。

## 完善代码

### items.py

我们要抓取这个网页的名人名言，作者和标签，首先要在items.py文件下定义字段。

```
import scrapy  class QuotesItem(scrapy.Item):     # define the fields for your item here like:     # name = scrapy.Field()     text = scrapy.Field()     author = scrapy.Field()     tags = scrapy.Field()
```

### spider.py

在讲这部分代码时，先介绍一个强大的工具：shell，有了这个工具，在抓取网页内容的时候会更加得心应手。 在命令行输入：

```
scrapy shell http://quotes.toscrape.com/
```

这样就进入了命令行交互模式，做一些调试。 这是我做的一些简单调试，相信你也会充分利用这个工具。继续完善代码。

```
# -*- coding: utf-8 -*- import scrapy from quotes.items import QuotesItem  class QuoteSpider(scrapy.Spider):     name = "quote"     allowed_domains = ["quotes.toscrape.com"]     start_urls = ['http://quotes.toscrape.com/']      def parse(self, response):         item = QuotesItem()          quotes = response.css('.quote')         for quote in quotes:             text = quote.css('.text::text').extract_first()             author = quote.css('.author::text').extract_first()             tags = quote.css('.tags .tag::text').extract()              item['text'] = text             item['author'] = author             item['tags'] = tags              yield item
```

这样基本能看到抓取的结果了，不过只有第一页的内容，接下来我们要抓取所有页的内容。想要抓取下一页的内容也非常简单，只要在本页找到下一页的链接，生成下一页的链接后不断重复这个过程，直到最后一页停止抓取。 抓取所有页数的完整代码为：

```
# -*- coding: utf-8 -*- import scrapy from quotes.items import QuotesItem  class QuoteSpider(scrapy.Spider):     name = "quote"     allowed_domains = ["quotes.toscrape.com"]     start_urls = ['http://quotes.toscrape.com/']      def parse(self, response):         item = QuotesItem()          quotes = response.css('.quote')         for quote in quotes:             text = quote.css('.text::text').extract_first()             author = quote.css('.author::text').extract_first()             tags = quote.css('.tags .tag::text').extract()              item['text'] = text             item['author'] = author             item['tags'] = tags              yield item          next = response.css('.pager .next a::attr(href)').extract_first()         url = response.urljoin(next)         yield scrapy.Request(url=url, callback=self.parse)
```

解释一下新增的三行代码。第一行用来找到下一页的超链接；第二行生成一个绝对的 URL，第三行使用 Request 方法，传入新生成的 url，使用回调来递归调用 parse 函数解析新生成的 url。运行以后就能采集所有页的名人名言了。

## 保存结果

### 保存到本地文件

抓取好网页的内容如何保存呢？可以使用 scrapy 的命令保存成多种文件格式。 输入命令：

```
scrapy crawl quote -o quotes.json
```

运行以后会生成一个 json 文件，保存了刚才我们抓取到的所有内容。

### 保存到数据库

在保存到数据库前，我们先要对抓取到的文本做一些处理，如果名人名言的长度大于 50，那就切断并显示为省略号。实现也很简单，要用到 pipelines.py 文件。 处理文本的代码为：

```
from scrapy.exceptions import DropItem  class TextPipeline(object):      def __init__(self):         self.limit = 50      def process_item(self, item, spider):         if item['text']:             if len(item['text']) > self.limit:                 item['text'] = "".join([item['text'][0:self.limit].strip(), "..."])                 return item         else:             return DropItem('Missing Text.')
```

然后在 settings.py 文件中开启。

```
ITEM_PIPELINES = {     'quotes.pipelines.TextPipeline': 300, }
```

这样设置以后，运行得到的结果就是我们已经处理后的结果了。接下来我们就要保存到数据库中。 我们先在 setting.py 文件中插入 mongo 数据库的相关信息：

```
MONGO_URI = 'localhost' MONGO_DB = 'quotes'
```

回到 piplines.py 文件编辑：

```
import pymongo  class MongoPipline(object):      def __init__(self, mongo_uri, mongo_db):         self.mongo_uri = mongo_uri         self.mongo_db = mongo_db      @classmethod     def from_crawler(cls, crawler):         return cls(             mongo_uri=crawler.settings.get('MONGO_URI'),             mongo_db=crawler.settings.get('MONGO_DB')         )      def open_spider(self, spider):         self.client = pymongo.MongoClient(self.mongo_uri)         self.db = self.client[self.mongo_db]      def process_item(self, item, spider):         if self.db['quotes'].insert(dict(item)):             print("ok.")             return item
```

这段代码实现的功能是传入参数后，获取 mongo 数据库的配置信息，然后在 spider 运行前开启 mongo 服务，运行过程中插入到数据库。 再次运行后打开 mongo 数据库，就能看到处理过的文本信息都已经保存好了。

# 总结

本文涉及的代码见 [github](https://github.com/alpha87/quotes)。 从这个简单的项目中我们就能体会到scrapy的强大。不过这只是冰山一角，需要我们学习的内容还有很多很多，在之后的教程中会更加详细的介绍scrapy每一个模块的用法。 [scrapy](http://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/tutorial.html)[中文教程](http://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/tutorial.html) | [scrapy](https://doc.scrapy.org/en/latest/)[英文教程](https://doc.scrapy.org/en/latest/) 让我们共同进步！ :)