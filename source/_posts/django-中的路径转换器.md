---
title: Django 中的路径转换器
tags:
  - Django
  - Python
  - Web开发
id: '93'
categories:
  - - 编程学习
date: 2019-05-18 12:52:13
---

![](https://i.loli.net/2020/02/06/yUBWREsLAOYSwcQ.jpg) 在使用 Django 配置 URL 调度器的时候，多少会涉及到 URL 中参数的使用，那么 Django 中有多少路径转换器可以使用呢？
<!-- more -->
原文档可参见 [path-converters](https://docs.djangoproject.com/zh-hans/2.2/topics/http/urls/#path-converters) 默认情况下，以下路径转换器可用：

## str

匹配任何非空字符串，不包括路径分隔符'/'。 如果转换器未表示在表达式中，则这是默认值。

## path

匹配任何非空字符串，包括路径分隔符'/'。 这允许您匹配完整的 URL 路径，而不是像 str 一样匹配 URL 路径的一部分。

## int

匹配零或任何正整数。 返回一个int。

## slug

匹配由 ASCII 字母或数字组成的任何 slug 字符串，以及连字符和下划线字符。 例如：`building-your-1st-django-site`。

## uuid

匹配格式化的 UUID。 要防止多个 URL 映射到同一页面，必须包含短划线，并且字母必须为小写。 例如：`075194d3-6885-417e-a8a8-6c931e272f00`。