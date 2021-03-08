---
title: 看书与实践 | 初学 Python 心得
tags:
  - Python
  - 心得
id: '38'
categories:
  - - 编程学习
date: 2017-01-14 10:11:23
---

![](https://i.loli.net/2020/02/06/cSzEAg1QHUauTqO.jpg) 这段时间买了一些有关 Python 的书，每天一边看，一边写一些爬虫的代码，导致博文一直没有跟上进度，没有及时记录自己的学习状况。
<!-- more -->
从学习 Python 的第一天起，到现在也已经学习了将近5本 Python 的书籍了。两本电子书，三本纸质书。分别是《笨办法学 Python 》、[《简明 Python 教程》](https://bop.molun.net/)；《零基础学 Python 》、《 Python 语言及其应用》、《 Python 学习手册》。还有一本《 Python 核心编程（第二版）》没有开始看。这些书可是一本比一本厚。这些书是从这里看到的：[黄哥推荐学习 Python 10本好书。](https://zhuanlan.zhihu.com/p/21566058)为了扎实基础，不断反复学习 Python 的语法，数据结构等知识，我觉得反复学习基础知识并不是一种浪费时间的行为，扎实的基础在学习更深奥的知识时才能快速理解。 在看书的过程中，发现 Python 不仅是一门强大的语言，更是一个需要不断探索的巨大宝库，内容无穷无尽。每学习一段时间就感叹 Python 的强大和巨大。每一本 Python 书都有无穷的知识需要被挖掘，光读纸质书是远远不够的，在看书的过程中，作者都会推荐在使用 Python 库的时候先阅读库相关的文档。现在我主要学习爬虫，看的是[BeautifulSoup](http://beautifulsoup.readthedocs.io/zh_CN/latest/)和[Requests](http://cn.python-requests.org/zh_CN/latest/)库的官方文档。还好有中文版的，虽然现在的浏览器都有翻译功能，翻译的质量也不低，但是机器就是机器，翻译的时候会把代码一并翻译，看着很痛苦啊。现在看书的时候虽然没有及时更新博文，但是在学习的过程中遇到问题我会记录下来，等看完书再查资料解决，这种方法也有助于巩固知识，而且我现在手边 Python 的书也有几本了，有一些内容可以几本书同时比较学习，感觉很不错。 接下来就是练习写代码了。写代码一开始是一个很花费时间的事情，因为你得考虑写代码的思路，解决遇到各种各样的有趣的问题。我的第一个爬虫代码虽然只有30行左右，但是花费了我将近两个晚上一个白天的时间，结果还是好的，达到了我的预期效果。不过有一些细节还是需要注意的。在写代码的过程中，我发现有很多问题需要注意，如果你和我一样同样是新手，就当我给你提醒了。 写代码之前最重要的是要考虑清楚，这段代码写完以后，需要完成一个怎样的任务，然后把任务细分化。比如说我的第一个代码是批量下载图片，这也是爬虫新手经常练习的代码。思路要清晰：

1.  解析网站，利用浏览器的开发者模式查看网页代码
2.  分析图片`URL`所在的标签
3.  把`URL`对应的图片保存到本地

这就是我爬取图片的思路。然后解决每个问题。 首先要解决的是分析网站的`URL`：

```python
from urllib.request import urlopen
```

也可以配合使用`BeautifulSoup`，不过不是必须的：

```python
from bs4 import BeautifulSoup
```

导入这两个库以后就可以开始写代码了。因为一会儿还要使用正则表达式匹配，所以再导入`re`库

```python
import re
```

我们完成这个任务要分三大部分，第一步是解析网页，第二步利用正则找出我们要的内容，第三步就是保存到本地。

```python
def get_html():
    url = "https://movie.douban.com/chart"
    douban = urlopen(url)
    html = douban.read()
    html = html.decode("utf-8")
    return html
```

这段代码就是分析豆瓣电影的`URL`，运行以后控制台输出了网页的源代码。这就是第一步。 第二步我们需要找到图片的相关代码： !\[豆瓣图片代码\](https://img- blog.csdn.net/20170114172122476?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQV9sUGhh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```markup
<img src="https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2393044761.jpg" alt="驴得水" class=""/>
```

这段是我们在写正则表达式要用到的。 第二步，定义函数，用正则表达式筛选出图片对应的`URL`和电影名称。

```python
def get(html):
    reg = re.compile(r'<img src="(.*?)" alt="(.*?)" class=""/>')
    item = re.findall(reg, html)
    print(item)
    return item
```

这两步完整的代码是：

```python
from urllib.request import urlopen
import re

def get_html():
    url = "https://movie.douban.com/chart"
    douban = urlopen(url)
    html = douban.read()
    html = html.decode("utf-8")
    return html

def get(html):
    reg = re.compile(r'<img src="(.*?)" alt="(.*?)" class=""/>')
    item = re.findall(reg, html)
    print(item)
    return item

a = get_html()
get(a)
```

运行结果：

```bash
[('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2393044761.jpg', '驴得水'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2391556881.jpg', '夜行动物'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2402824160.jpg', '28岁未成年'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2407543903.jpg', '无名女尸'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2403319543.jpg', '萨利机长'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2370911211.jpg', '中邪'), ('https://img5.doubanio.com/view/movie_poster_cover/ipst/public/p2380677316.jpg', '湄公河行动'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2326264650.jpg', '总有一天'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2409793660.jpg', '大叔的爱'), ('https://img3.doubanio.com/view/movie_poster_cover/ipst/public/p2355993802.jpg', '会计刺客')]
```

这样就做到了图片地址和对应电影名称的筛选工作，注意这是一个列表。因为现在的能力有限，如果想要下载到本地还得创建一个新函数。 第三步，用`re`重新分析图片地址并下载图片：

```python
def download(html):
    reg = re.compile(r'<img src="(.*?)" alt=".*?" class=""/>')
    item = re.findall(reg, html)
    print(item)

    i = 0
    for photo_html in item:
        f = open(str(i) + ".jpg", "wb")
        req = urlopen(photo_html)
        buf = req.read()
        f.write(buf)
        print("正在下载中...")
        i += 1
        f.close()
    print("下载完成")
```

这段代码完成了图片的保存。正则表达式的工作是把图片的`URL`储存在列表里，`for`循环下载依次下载列表里的每一个文件，记得关闭文件。所以从豆瓣电影下载完整的电影海报的完整代码是：

```python
from urllib.request import urlopen
import re

def get_html():
    url = "https://movie.douban.com/chart"
    douban = urlopen(url)
    html = douban.read()
    html = html.decode("utf-8")
    return html

def get(html):
    reg = re.compile(r'<img src="(.*?)" alt="(.*?)" class=""/>')
    item = re.findall(reg, html)
    return item

def download(html):
    reg = re.compile(r'<img src="(.*?)" alt=".*?" class=""/>')
    item = re.findall(reg, html)
    print(item)

    i = 0
    for photo_html in item:
        f = open(str(i) + ".jpg", "wb")
        req = urlopen(photo_html)
        buf = req.read()
        f.write(buf)
        print("正在下载中...")
        i += 1
        f.close()
    print("下载完成")
a = get_html()
get(a)
download(a)
```

这是我爬虫的思路，代码很新手，也很好理解。我认为这段代码有很多可以改进的地方：正则表达式的运用，下载图片的代码都需要改进。 这是我第一个爬虫代码，后来我还写了爬取简书30日排行榜，豆瓣电影排行榜等代码，因为正则表达式的问题，还不能正确匹配到信息。正则表达式还是一个需要马上攻克的难题啊，匹配不正确就不能爬到信息。 路还很长很长。

# 路还很长很长。

更新：完成了正则匹配工作，成功爬到了简书30日排行文章的一部分。现在需要做的是解析网页获取未加载的内容。