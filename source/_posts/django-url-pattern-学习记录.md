---
title: Django URL Pattern 学习记录
tags:
  - Django
  - Python
  - Web开发
id: '90'
categories:
  - - 编程学习
date: 2019-05-18 11:50:34
---

![](https://i.loli.net/2020/02/06/yUBWREsLAOYSwcQ.jpg) 学习 Django 文档的 URL 部分，做些笔记。
<!-- more -->
# 使用正则表达式

使用`re_path()`来配置 URL 路径。 在 Python 正则表达式中，命名正则表达式组的语法是(`?P<name>pattern`)，其中`name`是名称，`pattern`是要匹配的模式。

```python
from django.urls import path, re_path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$', views.article_detail),
]
```

# URL pattern 去除冗余

使用 urlpatterns 的 `include` 模块可以用来去除 URLconf 中的冗余，其中某个模式前缀被重复使用。 例如：

```python
from django.urls import path
from . import views

urlpatterns = [
    path('<page_slug>-<page_id>/history/', views.history),
    path('<page_slug>-<page_id>/edit/', views.edit),
    path('<page_slug>-<page_id>/discuss/', views.discuss),
    path('<page_slug>-<page_id>/permissions/', views.permissions),
]
```

可以将上边代码块改为：

```python
from django.urls import include, path
from . import views

urlpatterns = [
    path('<page_slug>-<page_id>/', include([
        path('history/', views.history),
        path('edit/', views.edit),
        path('discuss/', views.discuss),
        path('permissions/', views.permissions),
    ])),
]
```