---
title: 做一个魔力盒视频下载器——含GUI
tags:
  - Python
  - 爬虫
  - 脚本
id: '85'
categories:
  - - 编程学习
date: 2017-03-16 11:44:38
---

![](https://cdn.pixabay.com/photo/2015/02/24/02/05/website-647013__480.jpg) 昨天抽空做了一个魔力盒视频下载器。可以实现的功能有随机搜索视频下载，和一个简单的界面。
<!-- more -->
# 思路

昨天在博客上放了一个魔力盒的视频，顺手查看了视频网址的源码，突然觉得，恩，应该做点什么，然后这个小程序就诞生了。 github地址：[https://github.com/alpha87/molihedownload.git](https://github.com/alpha87/molihedownload.git) 爬取到视频的信息，然后显示在用户界面上。 先来一张效果图： [![界面](http://olzlqlgy5.bkt.clouddn.com/%E9%AD%94%E5%8A%9B%E7%9B%92GUI.jpg)](http://olzlqlgy5.bkt.clouddn.com/%E9%AD%94%E5%8A%9B%E7%9B%92GUI.jpg) 其实很简单，动手试一下吧。:-)

# 代码

先把代码贴上来，然后一一介绍。

## 爬虫代码

```python
#!/usr/bin/env python

from urllib.request import urlopen
from lxml import etree
import random
import os

def getVideo():
    page = int(random.uniform(1,99999))
    video_url = "http://magicapi.vmovier.com/magicapiv2/video/shareview?id={}".format(page)
    print("视频ID：\n",page)
    html = urlopen(video_url).read().decode("utf-8")
    url_xpath = etree.HTML(html)
    #标题
    title = url_xpath.xpath("//*[@id='share-tag']/@sharetitle")[0]
    print("视频： \n",title)
    # 简介
    blurb = url_xpath.xpath("//*[@id='share-tag']/@sharedefaultdes")[1]
    if blurb == "??":
        print("简介： \n 暂无")
    else:
        print("简介： \n", blurb)
    # 标签
    label = url_xpath.xpath("//*[@class='num']/text()")[0]
    if label == "##":
        print("标签： \n 未分类")
    else:
        print("标签： \n", label)
    # 分享
    share = url_xpath.xpath("//*[@class='num']/text()")[1]
    print("分享： \n", share)
    #链接
    url = url_xpath.xpath("//*[@controls='controls']/@src")[0]
    if url == "":
        print("链接： \n","这可能是一个假视频. T^T\n")
    else:
        print("链接： \n",url)
    return title,url

def downloadVideo(title,url):
    with open(str(title)+".mp4",'wb') as video:
        try:
            videos = urlopen(url).read()
            video.write(videos)
            print("正在下载视频...\n")
            print("下载完成！")
            print("=" * 50)
        except:
            print("视频未下载!")
            video.close()
            os.remove(str(title)+".mp4")
            print("=" * 50)
            pass

i = 0
while i <3:
    a,b = getVideo()
    downloadVideo(a,b)
    i+=1
```

这段代码有很多不完善的地方，整体思路就是爬取到网页信息后，自动保存，然后循环3次函数。模拟魔力盒APP，运行后自动下载3个视频。不过下载速度取决于网速，运行的时候不要以为卡住了。

## GUI界面代码

这段代码简单的介绍了Tkinter的使用，详细还得去看官方教程。贴代码：

```python
#!/usr/bin/env python

from tkinter.scrolledtext import ScrolledText
from tkinter import *

root = Tk()
root.title("魔力盒视频下载器")
root.geometry()
text = ScrolledText(root,font=("微软雅黑",9))
text.grid()
button = Button(root,text='下载视频',font=("微软雅黑",12),command=start)
button.grid()
var1 = StringVar()
label = Label(root,font=("微软雅黑",10),fg='red',textvariable=var1)
label.grid()
var1.set("准备就绪")
root.mainloop()
```

这样就简单的做出了一个框架了，但是还没什么用，最重要的就是下一步，把这两段代码结合起来。

# 魔力盒视频下载器

完整代码：

```python
#!/usr/bin/env python

from tkinter.scrolledtext import ScrolledText
from urllib.request import urlopen
from tkinter import ttk
from lxml import etree
from tkinter import *
import threading
import random
import os

def downloadVideo():
    try:
        page = int(random.uniform(1, 9999))
        video_url = "http://magicapi.vmovier.com/magicapiv2/video/shareview?id={}".format(page)
        print("视频ID：\n", page)
        html = urlopen(video_url).read().decode("utf-8")
        url_xpath = etree.HTML(html)
        title = url_xpath.xpath("//*[@id='share-tag']/@sharetitle")[0];print("视频： \n", title)
        blurb = url_xpath.xpath("//*[@id='share-tag']/@sharedefaultdes")[1]
        if blurb == "??" or blurb == "":
            print("简介： \n 暂无")
            blurb = str("暂无")
        else:
            print("简介： \n", blurb)
        label = url_xpath.xpath("//*[@class='num']/text()")[0]
        if label == "##":
            print("标签： \n 未分类")
            label = str("未分类")
        else:
            print("标签： \n", label)
        share = url_xpath.xpath("//*[@class='num']/text()")[1]
        print("分享： \n", share)
        url = url_xpath.xpath("//*[@controls='controls']/@src")[0]
        if url == "":
            print("链接： \n", "这可能是一个假视频. T^T\n")
        else:
            print("链接： \n", url)
        text.insert(END,
                    "视频ID：" + str(page) + "\n" +
                    "视频：" + str(title) + "\n" +
                    "简介：" + str(blurb) + "\n" +
                    "标签：" + str(label) + "\n" +
                    "分享人数：" + str(share) + "\n" +
                    "链接：" + str(url) + "\n\n")
        button.state(['disabled'])
        with open(str(title)+".mp4",'wb') as video:
            var1.set("正在下载，请稍后...  下载速度取决于网速")
            try:
                print("正在下载视频...\n")
                videos = urlopen(url).read()
                video.write(videos)
                var1.set("视频下载完成")
                button.state(['!disabled'])
                print("下载完成！")
                print("=" * 50)
            except:
                video.close()
                os.remove(str(title)+".mp4")
                var1.set("这可能是一个假视频. T^T")
                button.state(['!disabled'])
                print("视频未下载!")
                print("=" * 50)
                pass
    except BaseException as e:
        print(e)
        var1.set("请检查网络！")
        text.insert(END,str("暂无内容，请稍后再试。\n错误代码："+str(e)))
def start():
    th = threading.Thread(target=downloadVideo)
    th.start()

root = Tk()
root.title("魔力盒视频下载器")
root.geometry()
text = ScrolledText(root,font=("微软雅黑",8))
text.grid()
var1 = StringVar()
button = ttk.Button(root,text='下载视频',command=start)
button.grid()
label = Label(root,font=("微软雅黑",10),fg='red',textvariable=var1)
label.grid()
var1.set("准备就绪")
root.mainloop()
```

**详细的介绍下这段代码：**

## 库的使用

抓取网页：

```python
from urllib.request import urlopen
from lxml import etree
```

`urllib`用来解析网页，`etree`用来使用xpath定位。 GUI界面：

```python
from tkinter.scrolledtext import ScrolledText
from tkinter import ttk
from tkinter import *
```

`ScrolledText`是文本滚动条，`ttk`用来导入按钮。 其他需要：

```
import threading
import random
import os
```

在本段代码中分别实现多线程运行，随机选取视频ID和删除错误文件。

## 功能介绍

1.断网的情况下会显示网络连接错误，并展示错误代码。 2.下载过程中按钮被禁用，且显示“正在下载，请稍后… 下载速度取决于网速”，下载完成后可继续下载。 4.正常情况下会显示视频ID，视频标题，简介，标签，分享人数和视频链接。 3.若产生的随机视频ID出错，会报错并自动删除错误文件。 [![img](http://olzlqlgy5.bkt.clouddn.com/%E4%B8%8B%E8%BD%BD%E5%AE%8C%E6%88%90.jpg)](http://olzlqlgy5.bkt.clouddn.com/%E4%B8%8B%E8%BD%BD%E5%AE%8C%E6%88%90.jpg) 要注意保存视频的地址就是这个py文件所在的同一地址哦。