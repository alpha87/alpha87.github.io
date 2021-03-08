---
title: Scrapy学习笔记（五）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '115'
categories:
  - - 编程学习
date: 2017-07-12 10:17:58
---

本篇介绍scrapy的item pipeline，具体操作要根据爬取的内容决定。
<!-- more -->
Item Pipline有很多用处，官方文档举了一些常用的例子。

> *   cleansing HTML data
> *   validating scraped data (checking that the items contain certain fields)
> *   checking for duplicates (and dropping them)
> *   storing the scraped item in a database

有了这个组件在实际过程中对数据的处理还是很方便的。接下来我们学习编写自己的item pipeline。

## process\_item(self, item, spider)

每个 item pipeline 组件都需要调用该方法,这个方法必须返回一个 Item (或任何继承类)对象, 或是抛出 DropItem 异常,被丢弃的 item 将不会被之后的 pipeline 组件所处理。

## open\_spider(self, spider)

当 spider 被开启时,这个方法被调用。

## close\_spider(spider)

当 spider 被关闭时,这个方法被调用

## from\_crawler(cls, crawler)

这个类方法，主要用来获取scrapy项目中的配置信息。

# Item pipeline 例子

举了两个常用的例子，一般来说够用了，官方文档还有其他例子。

## 爬虫获取到item进行操作。

首先判断item的price属性是否存在，如果存在就进行下一步操作，如果不存在就抛出异常。

```python
from scrapy.exceptions import DropItem

class PricePipeline(object):

    vat_factor = 1.15

    def process_item(self, item, spider):
        if item['price']:
            if item['price_excludes_vat']:
                item['price'] = item['price'] * self.vat_factor
            return item
        else:
            raise DropItem("Missing price in %s" % item)
```

## 去重

这个例子是一个可以去重的过滤器,丢弃那些已经被处理过的item。我们的item有唯一id,但是返回内容中有许多相同id：

```python
from scrapy.exceptions import DropItem

class DuplicatesPipeline(object):

    def __init__(self):
        self.ids_seen = set()

    def process_item(self, item, spider):
        if item['id'] in self.ids_seen:
            raise DropItem("Duplicate item found: %s" % item)
        else:
            self.ids_seen.add(item['id'])
            return item
```

先定义一个集合，如果id已经存在就删除，如果不存在就添加到集合中。

## 保存到mongo数据库

```python
import pymongo

class MongoPipeline(object):

    collection_name = 'scrapy_items'

    def __init__(self, mongo_uri, mongo_db):
        self.mongo_uri = mongo_uri
        self.mongo_db = mongo_db

    @classmethod
    def from_crawler(cls, crawler):
        return cls(
            mongo_uri=crawler.settings.get('MONGO_URI'),
            mongo_db=crawler.settings.get('MONGO_DATABASE', 'items')
        )

    def open_spider(self, spider):
        self.client = pymongo.MongoClient(self.mongo_uri)
        self.db = self.client[self.mongo_db]

    def close_spider(self, spider):
        self.client.close()

    def process_item(self, item, spider):
        self.db[self.collection_name].insert_one(dict(item))
        return item
```

这段代码利用`from_crawler`方法获取settings中的配置信息，在spider开启时连接到数据库，在spider关闭时间断开与数据库的连接，在执行pipline过程中将item保存到数据库中。

# 配置item pipline

在settings中找到`ITEM_PIPELINES`属性，写入类的名称和优先级。 `python ITEM_PIPELINES = { 'myproject.pipelines.PricePipeline': 300, 'myproject.pipelines.MongoPipeline': 400, } ​` 数值越小优先级越高，要注意数值的范围是**0～1000**。