---
title: Scrapy学习笔记（三）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '113'
categories:
  - - 编程学习
date: 2017-06-25 12:16:12
---

我们来聊聊scrapy中选择器的用法。
<!-- more -->
我们选用的测试网页是scrapy官网推荐的测试网页，结构非常简单，但足够我们用来学习其用法。

[https://doc.scrapy.org/en/latest/\_static/selectors-sample1.html](https://doc.scrapy.org/en/latest/_static/selectors-sample1.html)

![img](https://i.loli.net/2020/02/20/ZsTtpQcSNMqkUAz.png)

我们主要使用三种选择器，读者可以根据自己的学习情况和网页结构选择使用。选择器分别是：xpath、css和re。这三种选择器实现的功能相同，所以熟悉哪个用哪个就行，语法都比较简单。

网页源码

```html
<html>
 <head>
  <base href='http://example.com/' />
  <title>Example website</title>
 </head>
 <body>
  <div id='images'>
   <a href='image1.html'>Name: My image 1 <br /><img src='image1_thumb.jpg' /></a>
   <a href='image2.html'>Name: My image 2 <br /><img src='image2_thumb.jpg' /></a>
   <a href='image3.html'>Name: My image 3 <br /><img src='image3_thumb.jpg' /></a>
   <a href='image4.html'>Name: My image 4 <br /><img src='image4_thumb.jpg' /></a>
   <a href='image5.html'>Name: My image 5 <br /><img src='image5_thumb.jpg' /></a>
  </div>
 </body>
</html>
```

我们在scrapy shell中学习这三种选择器的用法，实时反馈抓取到的信息。

```bash
scrapy shell https://doc.scrapy.org/en/latest/_static/selectors-sample1.html
```

## 获取title

```bash
In [3]: response.xpath('//title/text()')
Out[3]: [<Selector xpath='//title/text()' data='Example website'>]

In [4]: response.css('title::text')
Out[4]: [<Selector xpath='descendant-or-self::title/text()' data='Example website'>]

In [5]: response.xpath('//title/text()').extract_first()
Out[5]: 'Example website'

In [6]: response.css('title::text').extract_first()
Out[6]: 'Example website'
```

## 获取href

```bash
In [21]: response.xpath('//div[@id="images"]/a/@href').extract()
Out[21]: ['image1.html', 'image2.html', 'image3.html', 'image4.html', 'image5.html']

In [22]: response.css('div a::attr(href)').extract()
Out[22]: ['image1.html', 'image2.html', 'image3.html', 'image4.html', 'image5.html']
```

还可以这么用：

```bash
In [24]: response.xpath('//div[@id="images"]').css('img::attr("src")')
Out[24]:
[<Selector xpath='descendant-or-self::img/@src' data='image1_thumb.jpg'>,
 <Selector xpath='descendant-or-self::img/@src' data='image2_thumb.jpg'>,
 <Selector xpath='descendant-or-self::img/@src' data='image3_thumb.jpg'>,
 <Selector xpath='descendant-or-self::img/@src' data='image4_thumb.jpg'>,
 <Selector xpath='descendant-or-self::img/@src' data='image5_thumb.jpg'>]

In [25]: response.xpath('//div[@id="images"]').css('img::attr("src")').extract()
Out[25]:
['image1_thumb.jpg',
 'image2_thumb.jpg',
 'image3_thumb.jpg',
 'image4_thumb.jpg',
 'image5_thumb.jpg']
```

## default

```bash
In [29]: response.xpath('//div[@id="images"]').css('img::attr("src")').extract_first(default="None")
Out[29]: 'image1_thumb.jpg'

In [30]: response.xpath('//div[@id="images"]').css('img::attr("srcq")').extract_first(default="None")
Out[30]: 'None'
```

使用default要注意只能在`extract_first`中使用。如果获取到信息，就正常显示，如果找不到内容就返回default定义的信息。

## 获取文本内容

```bash
In [35]: response.xpath('//a/text()').extract()
Out[35]: 
['Name: My image 1 ',
 'Name: My image 2 ',
 'Name: My image 3 ',
 'Name: My image 4 ',
 'Name: My image 5 ']

In [36]: response.css('a::text').extract()
Out[36]: 
['Name: My image 1 ',
 'Name: My image 2 ',
 'Name: My image 3 ',
 'Name: My image 4 ',
 'Name: My image 5 ']
```

## 根据属性定位

如果我们想获取属性名称包含`image`的超链接，就可以使用这种方法。

```bash
In [40]: response.xpath('//a[contains(@href, "image")]/@href').extract()
Out[40]: ['image1.html', 'image2.html', 'image3.html', 'image4.html', 'image5.html']

In [41]: response.css('a[href*=image]::attr(href)').extract()
Out[41]: ['image1.html', 'image2.html', 'image3.html', 'image4.html', 'image5.html']
```

还有：

```bash
In [42]: response.xpath('//a[contains(@href, "image")]/img/@src').extract()
Out[42]: 
['image1_thumb.jpg',
 'image2_thumb.jpg',
 'image3_thumb.jpg',
 'image4_thumb.jpg',
 'image5_thumb.jpg']

In [43]: response.css('a[href*=image] img::attr(src)').extract()
Out[43]: 
['image1_thumb.jpg',
 'image2_thumb.jpg',
 'image3_thumb.jpg',
 'image4_thumb.jpg',
 'image5_thumb.jpg']
```

## 使用re

```bash
In [52]: response.css('a::text').re('Name\:(.*)')
Out[52]: 
[' My image 1 ',
 ' My image 2 ',
 ' My image 3 ',
 ' My image 4 ',
 ' My image 5 ']

In [53]: response.css('a::text').re_first('Name\:(.*)').strip()
Out[53]: 'My image 1'
```

以上这些就是就是选择器的基本用法了，如果你熟悉 xpath,css,re 的语法，接受起来应该非常容易，只是个别语法适应scrapy就好。

在以后的爬取中要学会使用这些选择器，事半功倍。