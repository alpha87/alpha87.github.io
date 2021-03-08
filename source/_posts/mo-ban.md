---
title: Flask 模板
tags:
  - Flask
  - 模板
id: '194'
categories:
  - - Flask 教程
date: 2020-02-16 21:34:45
---

在真正开始创建应用之前，我们必须明确一点，视图函数要和模版分离才行。如果不分离，代码将变得难以维护，而且复用也将变得困难。
<!-- more -->
视图函数的作用是请求进来之后，代码对该请求作出相应的逻辑处理，将动态结果返回给模板；而模板的作用就是将收到的变量渲染到页面上。这样就完成了数据的处理与展示。

# Jinja

Jinja（神社⛩️）是一种现代且设计友好的 Python 模板语言。语法简单易理解。Flask 默认使用 Jinja 作为模板语言，所以在安装 Flask 的时候就已经安装好了，无需再次安装，当然如果你只是想单独使用 Jinja，可以使用 `pip install Jinja2` 来安装它。

# 渲染模板

Flask 会默认寻找 templates 文件夹的模板文件，目录树如下：

```bash
.
├── Pipfile
├── Pipfile.lock
├── app.py
└── templates
    └── index.html
```

Pipfile 和 Pipfile.lock 是 pipenv 生成的管理包的配置文件，如果你没有使用 pipenv 创建虚拟环境可以忽略。

不使用模板的 Flask 代码：

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Hello, World!"
```

使用模板后的 Flask 代码：

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html", msg="Hello, World!")
```

# 变量和逻辑运算

使用 Jinja 只需记住两个主要内容就可以：一个是变量名由 `{{ }}` 来引入，另一个是逻辑运算需要使用 `{% %}` 来操作。

我们给模板传入用户信息，并把所有用户信息展示出来：

```markup
{% for user in users %}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{% endfor %}
</ul>
```

模板中的 `for user in users` 就需要用 `{% %}` 包起来，变量 `user.url` 和 `user.username` 用 `{{ }}` 包起来。

Jinja 还可以识别 Python 的列表，字典结构，例如：

```markup
{{ user_list[1] }}
{{ user_list[idx] }}
{{ user_dict["name"] }}

{# 这部分是注释 #}
```

基本上记住这两个语法，在操作模板的时候就比较得心应手了。

# 使用 JavaScript

如果你想在模板中使用 JavaScript，比如说想配合 Vue 渲染页面，而 Vue 中的变量和 Jinja 中的变量都是使用 `{{ }}` 包裹，那该怎么办？

Jinja 中提供了 `{% raw %} ... {% endraw %}` 来保证模板和 JavaScript 不被混淆。

例如我想同时使用 Jinja 模板和 Vue 来渲染页面：

```markup
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Flask 测试</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
{{ msg }}

{% raw %}
<div id="app">
    {{ message }}
</div>
{% endraw %}

{% raw %}
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}
</body>
</html>
```

`{{ msg }}` 用来接收视图函数的 `msg` 变量，`{{ message }}` 接收 Vue `message` 的值。互不干扰，成功渲染到页面上。

![HTML 页面](https://i.loli.net/2020/02/15/QYJr9NIhRVnUjsH.png)

# 过滤器

过滤器的功能在于对变量按要求进行修改。例如首尾去掉空格；去除所有 HTML 标签；转义所有 HTML 标签等等。

使用过滤器也非常简单，在需要转换的变量名后添加管道符和过滤器名，如果过滤器需要参数，后接括号即可。

```markup
{{ 75 | float }}
{{ name | filter_name(*arg) }}
```

## 常用过滤器

### trim

去掉变量首尾空格。

### default

如果变量为 `None`，默认使用 `default` 传入的默认值来显示：

```markup
{{ username | default("李雷") }}
```

如果变量的值为假，那么使用 `default` 传入的值：

```markup
{{ '' | default('韩梅梅', True)
```

### int, float

```markup
{{ 42 | float }}
42.0

{{ 42.7 | int }}
42
```

### round

如果你还在为 `int` 过滤器直接切断小数点取值会疑惑，那么考虑使用 `round` 过滤器吧。

```markup
{{ 4.4 | round(method="common") }}

4.0

{{ 4.7 | round(method="common") }}

5.0

{{ 4.7 | round(method="floor") }}

4.0

{{ 4.2 | round(method="ceil") }}

5.0
```

### join

把传入的列表拼接起来。

```markup
{{ ["李雷", "韩梅梅"] | join("与") }}
李雷与韩梅梅
```

### length

相当于 `len()`。

### striptags

删除变量中的 HTML 标签。

### escape

变量中的 HTML 标签会被转义。

### safe

如果你确保传入的变量中 HTML 代码足够安全，可以使用 `safe` 过滤器，被过滤后的 HTML 代码会被浏览器执行。

Jinja 还有许多过滤器，具体可以[查看文档](https://jinja.palletsprojects.com/en/2.11.x/templates/#list-of-builtin-filters)

## 自定义过滤器

如果没有合适的过滤器，我们还可以自己创建，非常方便。

自定义一个简单的切片过滤器：

```python
def slice(string: str, _len: int):
    return string[:_len] + "..."

app = Flask(__name__)

# 注册到 Jinja 的过滤器列表
app.jinja_env.filters["slice"] = slice
```

使用：

```markup
{{ "李雷与韩梅梅与李雷" | slice(4) }}
```

# 宏

宏的作用可以理解为定义重复使用的函数。既然是为了重复使用，我们就需要单独创建一个文件来保存宏。

```markup
<!-- macros.html -->

{% macro get_info(data) %}
<h1>{{ data["title"] }} </h1>
<p>{{ data["desc"] }} </p>
{% endmacro %}
```

这个宏是将传入的字典按标题和描述渲染。

视图函数：

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html", msg={
        "title": "标题",
        "desc": "描述"
    })
```

模板使用

```markup
<!DOCTYPE html>
{% import 'macros.html' as macros %}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Flask 测试</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
{{ macros.get_info(msg) }}
</body>
</html>
```

在模板中使用的时候记得引入 macros.html 文件。

```markup
{% import 'macros.html' as macros %}
```

# 重复使用代码片段

在开发中经常遇到多次使用的代码片段，可以使用 `{% include 'HTML 文件' %}` 的方式来重复使用。

```markup
<!-- hello.html -->

<h1>Hello, World</h1>
```

使用：

```markup
<!-- index.html -->

{% include 'hello.html' %}
{% include 'hello.html' %}
{% include 'hello.html' %}
```

# 模板继承

例如我们开发一款博客应用，博客的首页，文章详情页，分类，归档等页面的风格都是一致的，有着相同的页首和页脚。如果不使用继承，每个页面都要写入相同的头部，会出现大量重复的代码。不利于代码更新和后期维护。

当前应用的目录树：

```bash
.
├── Pipfile
├── Pipfile.lock
├── app.py
└── templates
    ├── base.html
    ├── detail.html
    └── index.html
```

base.html 用来存放基础模板，其他页面均继承此页面。

```markup
<!-- base.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    {% block head %}
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %} - Flask 测试</title>
    {% endblock %}
</head>
<body>
{% block body %}
{% endblock %}
</body>
</html>
```

index.html 相当于首页，使用 `extends` 声明模板继承于 base.html 模板。

```markup
<!-- index.html -->

{% extends "base.html" %}

{% block head %}
{{ super() }}
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
{% endblock %}

{% block title %}
首页
{% endblock %}

{% block body %}
<h1>{{ msg["title"] }}</h1>
<p>{{ msg["desc"] }}</p>
{% endblock %}
```

在首页 index.html 中，有这么一段代码：

```markup
{% block head %}
{{ super() }}
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
{% endblock %}
```

其中的 `{{ super() }}` 和 Python 中的 super() 效果类似，都是显式继承。就是在继承 `{% block head %} ... {% endblock %}` 中的内容时，保留原有内容，如果不使用 `{{ super() }}`，base.html 模板中的片段将不被使用。

detail.html 相当于详情页。

```markup
<!-- detail.html -->

{% extends "base.html" %}

{% block title %}
用户
{% endblock %}

{% block body %}
<h1>Hello</h1>
{% endblock %}
```

视图函数：

```python
@app.route("/")
def index():
    return render_template("index.html", msg={
        "title": "标题",
        "desc": "描述"
    })

@app.route("/user")
def user_info():
    return render_template("detail.html")
```

宏，include，继承都体现了能复用的代码一定不要写第二次。

# 自定义错误页面

Flask 应用会默认渲染一个错误页面，但是在实际开发过程中，这样的页面和我们创建应用的页面主题很不搭调。

```python
@app.errorhandler(404)
def error_404(error):
    return render_template("404.html"), 404
```

注意装饰器填入错误码，函数要填入参数，在返回响应的时候也要填入错误码。

创建对应的 404.html 模板文件：

```markup
{% extends "base.html" %}

{% block title %}
404
{% endblock %}

{% block body %}
<h1>404 找不到页面！</h1>
{% endblock %}
```

运行 Flask 应用，随便请求一个不存在的页面，就会跳转到自定义 404 错误页面。