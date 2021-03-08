---
title: Flask 两款拓展插件使用记录
tags:
  - Bootstrap-Flask
  - Flask
  - flask-moment
id: '207'
categories:
  - - Flask 教程
date: 2020-02-17 21:49:30
---

这篇文章介绍两款 Flask 开发中常用的拓展，一款是高效简洁，用来构建页面的 Bootstrap-Flask，一款是在页面上可以多种风格显示时间的 Flask-Moment。
<!-- more -->
# Bootstrap-Flask

Bootstrap 是一款用于网站和网络应用程序开发的开源前端框架，包括 HTML、CSS 及 JavaScript 的框架，提供字体排印、窗体、按钮、导航及其他各种组件及 Javascript 扩展，旨在使动态网页和 Web 应用的开发更加容易（[维基百科](https://zh.wikipedia.org/zh-cn/Bootstrap)）。

我们可以使用现成的 Flask 插件来使用 Bootstrap。但是由于许多教程中提到的 Flask-Bootstrap 已经很久没有维护，且只支持 Bootstrap 3，所以更换为 [Bootstrap-Flask](https://github.com/greyli/bootstrap-flask)，基于 Flask-Bootstrap 但更好用更轻量，同时支持 Bootstrap 4。

## 安装

```bash
pipenv install bootstrap-flask
```

## 使用

Bootstrap-Flask 使用起来也很简单方便，我们需要自己创建基础模板，并在基础模板中引入 css 和 JavaScript 资源。和 Flask-Bootstrap 不同，我们不用继承其生成的 base.html 模板，我们可以使用自己的 base.html 模板，只需导入资源，大大提升了网页开发的灵活性。

app.py 代码如下：

```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap

app = Flask(__name__)
bootstrap = Bootstrap(app)

@app.route("/")
def index():
    return render_template("index.html")
```

base.html 代码如下：

```markup
<!DOCTYPE html>
<html lang="en">
<head>
    {% block head %}
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <title>{% block title %}{% endblock %} - Flask 测试</title>
    {% endblock %}
    {{ bootstrap.load_css() }}
</head>
<body>
{{ bootstrap.load_js() }}
<div class="container">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="{{ url_for('index') }}">Flask 测试</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav">
                <li class="nav-item active">
                    <a class="nav-link" href="{{ url_for('index') }}">首页</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{{ url_for('index') }}">留言板</a>
                </li>
            </ul>
        </div>
    </nav>
    {% block body %}
    {% endblock %}
</div>
</body>
</html>
```

和之前的文章相比，突然多了很多代码，但是不用担心，引入 bootstrap 的关键代码在 head 和 body 中：

```markup
<head>
    ...
    {{ bootstrap.load_css() }}
</head>
<body>
    {{ bootstrap.load_js() }}
    ...
</body>
```

至于其他部分，是使用 bootstrap 创建了导航栏。

![首页](https://i.loli.net/2020/02/17/ZX8WGTyplUfmzI2.png)

当我们看到导航栏，说明我们已经成功使用 Bootstrap-Flask。

例如我们在首页创建登录页面：

![登录页面](https://i.loli.net/2020/02/17/6TMFPdAhrLcQnlE.png)

其他组件的使用完全可以参照 bootstrap 4 的文档来构建页面。

# Flask-Moment

这款插件是将 [Moment.js](https://momentjs.com/) 整合到 Flask 中，Moment.js 可以让时间按各种各样的样式呈现到页面上。

## 为什么要使用 Flask-Moment

我们知道，网站在搭建以后，面对的用户来自世界各地，由于时区差异，显示时间变得棘手。有没有什么办法能实现虽然地理位置不同，但是有统一的时间展示？

UTC（协调世界时间）可以帮助我们解决这个问题，但是如果直接使用 UTC，会让用户非常困惑，因为其显示的可能并不是自己所在时区的时间，所以我们用到 [Flask-Moment](https://github.com/miguelgrinberg/Flask-Moment)，帮助我们将时间显示的更加友好，美观。

## 安装

```bash
pipenv install flask-moment
```

## 使用

只需三步走：

在 app.py 中引入 flask-moment：

```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap
from flask_moment import Moment
from datetime import datetime

app = Flask(__name__)
bootstrap = Bootstrap(app)
moment = Moment(app)

@app.route("/")
def index():
    return render_template("index.html", _time=datetime.utcnow())
```

在 base.html 的 head 标签中导入 moment.js 资源：

```markup
<head>
    ...
    {{ moment.include_jquery() }}
    {{ moment.include_moment() }}
    {{ moment.locale("zh-cn") }}
</head>
```

前两行为导入资源，第三行设置显示为中文。

在页面中使用：

```markup
{% extends "base.html" %}

{% block title %}
首页
{% endblock %}

{% block body %}

<p>当前时间：{{ moment(_time).format("llll") }}</p>
<p>已经过去：{{ moment(_time).fromNow(refresh=True) }}</p>

{% endblock %}
```

使用效果：

![时间显示](https://i.loli.net/2020/02/17/xkvVNeI3TY4hM6w.png)

指定 `refresh=True` 参数后，内容会随着时间更新，一分钟刷新一次。

## 多种时间显示

```javascript
moment().format('LT');   // 21:38
moment().format('LTS');  // 21:38:14
moment().format('L');    // 2020/02/17
moment().format('l');    // 2020/2/17
moment().format('LL');   // 2020年2月17日
moment().format('ll');   // 2020年2月17日
moment().format('LLL');  // 2020年2月17日晚上9点38分
moment().format('lll');  // 2020年2月17日 21:38
moment().format('LLLL'); // 2020年2月17日星期一晚上9点38分
moment().format('llll'); // 2020年2月17日星期一 21:38
```

其他参数可参考 [moment.js](https://momentjs.com/) 官网介绍。