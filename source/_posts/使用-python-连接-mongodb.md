---
title: 使用 Python 连接 mongoDB
tags:
  - mongoDB
  - Python
id: '58'
categories:
  - - 编程学习
date: 2017-02-28 10:47:56
---

![](https://upload.wikimedia.org/wikipedia/commons/0/00/Mongodb.png) 之前使用的MySQL是关系型数据库，mongoDB是非关系型数据库（NoSQL）。
<!-- more -->
## 介绍mongoDB

> MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

以上介绍来自[百度百科mongoDB](http://baike.baidu.com/link?url=z8OHE7KbfYKiSxtk82Pj9Uvu22vcLHvnMmM5sgoEUPDCckS-tSku2oeknp3hOqgkQbx2UFvFs8sUXaVdaSt52q)。:-)

## 安装pymongo

在终端输入： `pip install pymongo -i https://pypi.tuna.tsinghua.edu.cn/simple` 等待安装完成。这里我使用了清华源国内镜像加速下载，安装其他第三方库也可以使用。

## 基本命令

使用mongoDB，还需要了解一些基本命令。

#### 创建或使用数据库

`use dbname;`

#### 查看数据库信息:

`show dbs;`

#### 查看数据库中的集合（也就是关系型数据库的表）:

`show collections;`

#### 创建数据库中的集合（表）:

`db.createCollection(name);`

#### 删除数据库中的集合（collectionname是要删除的集合）:

`db.collectionname.drop();`

#### 删除数据库，先use需要删除的数据库，然后输入：

`db.dropDatabase();`

#### 查询所有记录，默认每页显示20条数据:

`db.collectionname.find();`

#### 设置每页显示数据的数量，显示100条记录。查看下一页键入it命令，不用输入;

`DBQuery.shellBatchSize=100;`

#### 去重，过滤collectionname中name的相同数据:

`db.collectionname.distinct('name');`

## 使用Python

连接mongoDB和插入数据的代码比较简单:

```python
import pymongo


connection = pymongo.MongoClient('127.0.0.1', 27017)
tdb = connection.alpha87
post = tdb.test
post.insert({'name':"李白", "age":"30", "skill":"Python"})
print("操作完成")
```

我们来一行一行分析。 导入pymongo库： `import pymongo` 连接mongoDB，因为是在本机操作，所以圆括号中的 '127.0.0.1', 27017可写可不写。 ·connection = pymongo.MongoClient('127.0.0.1', 27017)· 命名数据库为 alpha87: `tdb = connection.alpha87` 创建 test 集合，也就是表: `post = tdb.test` 插入数据: `post.insert({'name':"李白", "age":"30", "skill":"Python"})` 运行以后，就能在数据库中查看到保存好的数据了。 **因为是字典格式，所以数据保存后的顺序可能和插入时不同。**