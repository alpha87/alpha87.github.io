---
title: Scrapy学习笔记（六）
tags:
  - Python
  - Scrapy
  - 爬虫
id: '112'
categories:
  - - 编程学习
date: 2017-07-15 12:15:16
---

本文是介绍Scrapy基础用法的最后一篇，介绍Scrapy中Download Middleware的用法。
<!-- more -->
Download Middleware是介于Scrapy的Request/Response处理的钩子框架。是用于全局修改Scrapy Request和Response的一个轻量、底层的系统。

# 编写中间件

中间件定义了一些方法，只要我们了解这种方法以后，也就可以自己编写中间件了。

## Process\_request(Request, Spider)

> 每个Request通过Download Middleware时,该方法被调用。Process\_request()必须返回其一：None；Response；Request；Raise Ignorerequest。 如果返回None，Scrapy将继续处理该Request，执行其他的中间件的相应方法,直到合适的下载器处理函数被调用,该Request被执行。 如果返回Response对象,Scrapy将不会调用任何其他的Process\_request()或Process\_exception()方法,或相应地下载函数; 其将返回该 Response。已安装的中间件的 Process\_response()方法则会在每个Response返回时被调用。 如果其返回Request对象,Scrapy则停止调用Process\_request方法并重新调度返回的 Request。当新返回的Request被执行后, 相应地中间件链将会根据下载的Response被调用。 如果其 Raise 一个 Ignorerequest 异常,则安装的下载中间件的 Process\_exception() 方法会被调用。如果没有任何一个方法处理该异常, 则Request的Errback(Request.Errback)方法会被调用。如果没有代码处理抛出的异常, 则该异常被忽略且不记录(不同于其他异常那样)。

如果在爬取过程中想要添加代理，我们可以通过这个方法。

## Process\_response(Request, Response, Spider)

> Process\_request()必须返回以下之一：返回一个 Response 对象、返回一个 Request 对象或 Raise 一个 Ignorerequest 异常。 如果其返回一个 Response (可以与传入的Response相同,也可以是全新的对象)该Response 会被在链中的其他中间件的 Process\_response()方法处理。 如果其返回一个 Request 对象,则中间件链停止,返回的 Request 会被重新调度下载。处理类似于 Process\_request()返回 Request 所做的那样。 如果其抛出一个Ignorerequest异常,则调用Request的Errback(Request.Errback)。如果没有代码处理抛出的异常,则该异常被忽略且不记录(不同于其他异常那样)。

我们可以利用这个方法修改爬取网页的状态码。

## Process\_exception(Request, Exception, Spider)

当下载处理器(Download Handler)或 Process\_request()(下载中间件)抛出异常(包括 Ignorerequest异常)时,Scrapy 调用 Process\_exception()。

> Process\_exception()应该返回以下之一: 返回 None 、一个 Response 对象、或者一个 Request 对象。 如果其返回 None ,Scrapy 将会继续处理该异常,接着调用已安装的其他中间件的Process\_exception()方法,直到所有中间件都被调用完毕,则调用默认的异常处理。 如果其返回一个 Response 对象,则已安装的中间件链的 Process\_response()方法被调用。Scrapy 将不会调用任何其他中间件的 Process\_exception() 方法。 如果其返回一个 Request 对象, 则返回的 Request 将会被重新调用下载。这将停止中间件的 Process\_exception()方法执行,就如返回一个 Response 的那样。

这个方法可以在爬虫过程中出现异常提供解决办法。如果出现异常，可以使用该中间件进行下一步操作。