---
title: 学习mongoDB（二）
tags:
  - mongoDB
  - pymongo
id: '76'
categories:
  - - 编程学习
date: 2017-03-02 11:32:00
---

![](https://upload.wikimedia.org/wikipedia/commons/0/00/Mongodb.png) 这篇文章主要介绍了查找集合的命令。如何查找所有集合，如何指定条件查找数据，使用运算符查找，查询结果排序等。
<!-- more -->
# 查找

查找分为两部分，第一部分就是查找数据库中的所有集合；第二部分就是指定条件查询条件匹配的集合，条件可以以参数的形式传递给find()方法。

## 查询集合中的所有文档

按照[上一篇文章](https://alpha87.github.io/2017/03/01/%E5%AD%A6%E4%B9%A0mongoDB%EF%BC%88%E4%B8%80%EF%BC%89/)的顺序，我们已经插入好了数据。我们就以上文中的数据为例。 在连接好数据库以后，输入：

```
coll = db.test.find()
```

查询所有文档，然后迭代打印出集合的内容：

```python
for information in coll:
    print(information)
```

# 指定条件

## 指定已知数据

如果你知道需要查找数据的信息，可以以如下的格式传入到find()方法，作为参数。

```
(<field1>:<value1>, <field2>:<value2>, ...)
```

例如：

```
coll = db.test.find({"borough":"Manhattan"})
```

然后打印：

```python
for doc in coll:
    print(doc)
```

## 使用操作符指定条件

mongoDB提供运算符来指定查询条件，比如比较运算符。使用运算符的查询条件通常有以下形式：

```
{<field1>:{<operator1>:<value1>}}
```

有关运算符的完整列表，请参见[查询运算符](http://docs.mongodb.com/manual/reference/operator/query)。

### 大于运算符($gt)

查询其成绩数组包含字段得分大于30的文档。

```
coll = db.test.find({"grades.score":{"$gt":30}})
```

### 小于运算符($lt)

查询其成绩数组包含字段得分小于10的文档。

```
coll = db.test.find({"grades.score":{"$lt":10}})
```

## 组合条件

您可以在逻辑连接（AND）和逻辑析取（OR）中组合多个查询条件。

### 逻辑和 (and)

```
coll = db.test.find({"cuisine":"ltalian","address.zipcode":"10075"})
```

### 逻辑或(or)

```
coll = db.test.find({"$or":[{"cuisine":"ltalian"},{"address.zipcode":"10075"}]})
```

# 查询结果排序

如果要为查询的集合指定顺序，使用sort()方法追加到查询中。 pymongo.ASCENDING 用于升序，pymongo.DESCENDING 用于降序。 文档提供的例子： `import pymongo cursor = db.restaurants.find().sort([ ("borough", pymongo.ASCENDING), ("address.zipcode", pymongo.ASCENDING) ])`