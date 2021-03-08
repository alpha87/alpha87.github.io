---
title: 学习mongoDB（一）
tags:
  - mongoDB
  - pymongo
id: '77'
categories:
  - - 编程学习
date: 2017-03-01 11:33:31
---

![](https://upload.wikimedia.org/wikipedia/commons/0/00/Mongodb.png) 决定要系统学习在Python中使用mongoDB，那就从看文档开始。
<!-- more -->
这篇文章主要介绍了如何安装pymongo，如何在Python中连接mongoDB，并创建数据库，创建集合，插入数据。

# MongoDB简介

[MongoDB](https://docs.mongodb.com/manual/)是一个开源的_文档数据库_，提供高性能，高可用性和自动扩展。MongoDB不需要对象关系映射（ORM）来促进开发。

# PyMongo

## 介绍

pymongo是Python的第三方库，其中包含用于处理MongoDB的工具，通过Python处理MongoDB。

## 安装

在cmd中输入命令：

```
pip install pymongo
```

安装好以后，在Python交互式解释器输入：

```
import pymongo
```

如果没有提示错误说明安装成功。

## 连接

在Python中使用mongoDB，首先要导入库。导入以后使用MongoClient创建连接：

```
client = MongoClient()
```

圆括号中的参数可写可不写，如果要加入参数可以输入：

```
client = MongoClient('127.0.0.1', 27017)
```

也可以使用官方文档中提供的方法：

```
client = MongoClient("mongodb://mongodb0.example.net:27017")
```

## 访问数据库对象

在创建好连接以后，可以访问原有的数据库，也可以在代码中创建新的数据库。例如我们要新创建一个数据库，名为“primer”，可以输入：

```
db = client.primer
```

或

```
db = client['primer']
```

看到这里你一定发现了，你可以像使用Python中的字典一样访问数据库。

## 访问集合对象

MongoDB将文档存储在集合中。集合类似于关系数据库中的表。但是，与表不同，集合不需要其文档具有相同的模式，意味着可以插入不同格式的数据。在MongoDB中，存储在集合中的文档必须有一个唯一的\_id 字段作为**主键**。 输入：

```
coll = db.test
```

或

```
coll = db['test']
```

这样操作下来，你的mongoDB中就创建了新的数据库，数据库的名字是primer，在这个数据库下有一个集合，名字是test。

## 插入数据

按照官方文档的提示，我们插入和文档中相同的数据：

```python
coll.insert_one(
    {
        "address": {
            "street": "2 Avenue",
            "zipcode": "10075",
            "building": "1480",
            "coord": [-73.9557413, 40.7720266]
        },
        "borough": "Manhattan",
        "cuisine": "Italian",
        "grades": [
            {
                "date": datetime.strptime("2014-10-01", "%Y-%m-%d"),
                "grade": "A",
                "score": 11
            },
            {
                "date": datetime.strptime("2014-01-16", "%Y-%m-%d"),
                "grade": "B",
                "score": 17
            }
        ],
        "name": "Vella",
        "restaurant_id": "41704620"
    }
)
```

该操作返回一个InsertOneResult对象，其中包含一个包含所插入文档的\_id的属性inserted\_id。访问此属性：

```
coll.inserted_id
```

输出：

```
ObjectId("54c1478ec2341ddf130f62b7")
```

如果你没有主动设置主键，该id会自动生成。