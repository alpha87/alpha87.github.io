---
title: BeautifulSoup4 文档学习记录
tags:
  - BeautifulSoup4
  - Python
id: '87'
categories:
  - - 编程学习
date: 2017-01-19 11:48:10
---

![](https://cdn.pixabay.com/photo/2017/08/10/08/47/code-2620118__480.jpg) 花了点时间阅读了 Beautiful Soup 的官方文档。说实话，之前看到这些东西就头疼，密密麻麻全是字，真是不想看，不过没办法，东西总是要学习的，哪能说不看就不看呢。然后越看越有意思，因为能学到新东西嘛。 Beautiful Soup 是一个可以从 HTML 或 XML 文件中提取数据的 Python 库。Beautiful Soup 将复杂 HTML 文档转换成一个复杂的树形结构，每个节点都是 Python 对象，所有对象可以归纳为4种：Tag，NavigableString，BeautifulSoup，Comment。以我现在对 Beautiful Soup 的认识，我认为它就是一个可以把 HTML 标签整理，筛选等工作完成的工具，就像官方文档说的一样：“它能够通过你喜欢的转换器实现惯用的文档导航，查找，修改文档的方式。” 这是文档提供的代码：

```python
html_doc = """
<html>
  <head>
    <title>The Dormouse's story</title></head>
  <body>
    <p class="title">
      <b>The Dormouse's story</b>
    </p>
    <p class="story">Once upon a time there were three little sisters; and their names were
      <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>，
      <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a>and
      <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;and they lived at the bottom of a well.</p>
    <p class="story">...</p>
"""
```

在这段代码中，一般网页有的标签这里都基本涉及到了。

# 从文档中找到所有`<a>`标签的链接

```python
for link in soup.find_all('a'):
    print(link.get('href'))  #get（标签属性）就能获取标签属性对应的内容

# http://example.com/elsie
# http://example.com/lacie
# http://example.com/tillie
```

# 从文档中获取所有文字内容

```python
print(soup.get_text())

#soup必须是字符串格式才行

# The Dormouse's story
## The Dormouse's story
## Once upon a time there were three little sisters; and their names were
# Elsie，
# Lacie and# Tillie;
# and they lived at the bottom of a well.
## ...
```

# 每个tag都有自己的名字，通过`.name`来获取:

```bash
tag.name
# u'b'
```

# `.attrs` #用来获取标签属性 :

```bash
tag.attrs
# {u'class': u'boldest'}
```

# 操作文档树最简单的方法就是告诉它你想获取的`tag`的`name`.如果想获取 `<head>` 标签，只要用 `soup.head`

```python
soup.head
# <head><title>The Dormouse's story</title></head>

soup.title
# <title>The Dormouse's story</title>
```

# 这是个获取`tag`的小窍门，可以在文档树的`tag`中多次调用这个方法.下面的代码可以获取`<body>`标签中的第一个`<b>`标签

```python
soup.body.b

# <b>The Dormouse's story</b>
```

# `tag`的`.contents` 属性可以将tag的子节点以「列表」的方式输出

```python
head_tag.contents

# [<title>The Dormouse's story</title>]
```

# 输出的字符串中可能包含了很多空格或空行，使用`.stripped_strings`可以去除多余空白内容

```python
for string in soup.stripped_strings:
    print(repr(string))
```

# 查找文档中所有的**标签**

```python
soup.find _all('b') # 查找<b>标签

# [<b>The Dormouse's story</b>]
```

# 如果传入列表参数，Beautiful Soup会将与列表中任一元素匹配的内容返回

下面代码找到文档中所有`<a>`标签和`<b>`标签：

```python
soup.find_all(["a"， "b"])

# [<b>The Dormouse's story</b>，#  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>，#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>，#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

# `find_all( name ， attrs ， recursive ， string ， **kwargs )`

## `name` 参数可以查找所有名字为 `name` 的`tag`，字符串对象会被自动忽略掉.

```python
简单的用法如下:
soup.find_all("title")

# [<title>The Dormouse's story</title>]
```

## keyword 参数

如果包含一个名字为 id 的参数，Beautiful Soup 会搜索每个 tag 的 id 属性。（不只是id，其他标签同样适用）。

```python
soup.find_all(id='link2')

# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

如果传入 href 参数，Beautiful Soup会搜索每个tag的”href”属性:

```python
soup.find_all(href=re.compile("elsie"))

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]
```

使用多个指定名字的参数可以同时过滤tag的多个属性:

```python
soup.find_all(href=re.compile("elsie")， id='link1')

# [<a class="sister" href="http://example.com/elsie" id="link1">three</a>]
```

# 通过 `find_all()` 方法的 `attrs` 参数定义一个字典参数来搜索包含特殊属性的`tag`

```python
data_soup.find_all(attrs={"data-foo": "value"}) #标签等于标签的属性

# [<div data-foo="value">foo!</div>]
```

# 通过 `string` 参数可以搜索文档中的字符串内容

```python
soup.find_all(string=re.compile("Dormouse"))

# [u"The Dormouse's story"， u"The Dormouse's story"]
```

## 虽然 `string` 参数用于搜索字符串，还可以与其它参数混合使用来过滤`tag`，可以用来过滤标签

Beautiful Soup会找到 .string 方法与 string 参数值相符的tag.下面代码用来搜索内容里面包含“Elsie”的标签:

```python
soup.find_all("a"， string="Elsie")

# [<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>]
```

`find_all()` 几乎是 Beautiful Soup 中最常用的搜索方法，所以我们定义了它的简写方法. BeautifulSoup 对象和 `tag` 对象可以被当作一个方法来使用，这个方法的执行结果与调用这个对象的 `find_all()` 方法相同，下面两行代码是等价的:

```python
soup.find_all("a")
soup("a")
```

这两行代码也是等价的:

```python
soup.title.find_all(string=True)
soup.title(string=True)
```

## 通过 `tag` 标签逐层查找，根据标签逐层查找

```python
soup.select("body a")

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>，#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>，#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

```python
soup.select("html head title") 

# 先是<html>然后是其下的<head>然后是<head>下的<title>标签，层层向下
# [<title>The Dormouse's story</title>]
```

找到某个tag标签下的直接子标签:（可以利用此方法搜索较大标签下的子标签）

```python
soup.select("head > title")  # 搜索父标签下的子标签内容

# [<title>The Dormouse's story</title>]
```

```python
soup.select("p > a") 

# 搜索<p>标签下的<a>标签内容
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>，#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>，#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

```python
soup.select("p > a:nth-of-type(2)") 

# 应该是搜索<p>标签下，<a>标签属性数量为3的<a>标签
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

```python
soup.select("p > #link1") # 搜索<p>标签下属性值为link1的标签内容

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]
```

```python
soup.select("body > a")

# []
```

## 通过`tag`的`id`查找

```python
soup.select("#link1")

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]
```

```python
soup.select("a#link2")

# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

同时用多种CSS选择器查询元素:

```python
soup.select("#link1，#link2")

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>，#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

通过是否存在某个属性来查找:

```python
soup.select('a[href]')

# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>，#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>，#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

通过属性的值来查找:

```python
soup.select('a[href="http://example.com/elsie"]')
```

## 返回查找到的元素的第一个

```python
soup.select_one(".sister")

# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
```

## `get_text()`

如果只想得到 tag 中包含的文本内容，那么可以用 `get_text()` 方法，这个方法获取到tag中包含的所有文版内容包括子孙 tag 中的内容，并将结果作为 Unicode 字符串返回。 通过参数指定 `tag` 的文本内容的分隔符:

```python
soup.get_text("|")u'\nI linked to |example.com|\n'
```

还可以去除获得文本内容的前后空白:

```python
soup.get_text("|"， strip=True)u'I linked to|example.com'
```

我觉得有用的就这么多了，总结的有些粗糙，不过我觉得如果你用过`beautiful soup`的话应该是能理解的，其他的一些内容可以看官方文档，很详细很全。 [Beautiful Soup 4.4.0官方文档](beautifulsoup.readthedocs.io/zh_CN/latest/)