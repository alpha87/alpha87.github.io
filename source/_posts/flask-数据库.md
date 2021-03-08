---
title: Flask 数据库
tags:
  - Flask
  - MongoEngine
  - SQLAlchemy
  - 数据库
id: '279'
categories:
  - - Flask 教程
date: 2020-02-26 16:26:51
---

数据库一般又被称为数据管理系统（Database Management System，简称 DBMS）。分为两大类，关系型数据库（SQL）和非关系型数据库（NoSQL）。关系型数据库是我们常见的 MySQL，SQLite 等。NoSQL 是对不同于传统的关系数据库的数据库管理系统的统称。两者存在许多显著的不同点，其中最重要的是 NoSQL 不使用 SQL 作为查询语言。例如 Redis 使用键值对保存数据，MongoDB 类似 JSON 结构保存数据。对于中小型程序来说，关系型数据库和非关系型数据库性能相当，哪个方便开发就选择哪个。
<!-- more -->
本文会使用 SQLAlchemy 和 MongoEngine 来开发一款简易留言板。通过这个小项目来学习 Flask 数据库的使用。

# Flask-SQLAlchemy

SQLAlchemy 是 Python 编程语言下的一款开源软件。提供了 SQL 工具包及对象关系映射工具，使用 MIT 许可证发行。 SQLAlchemy 采用简单的 Python 语言，为高效和高性能的数据库访问设计，实现了完整的企业级持久模型。（[维基百科](https://zh.wikipedia.org/zh-cn/SQLAlchemy)）

简单来说，SQLAlchemy 封装了数据库操作接口，把 SQL 语句用 Python 语言描述出来。我们可以通过 Python 来操作数据库，不用直接输入 SQL 语句，提升了开发效率。而 [Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/) 是对 SQLAlchemy 在 Flask 中又一层集成，让我们在 Flask 中操作数据库更加方便高效。

# 安装

```bash
pipenv install flask-sqlalchemy
```

# 在连接数据库之前

安装好 `flask-sqlalchemy` 之后，我们需要创建一个崭新的表单，类似 Wordpress 默认的留言板样式。通过这个表单小项目来学习数据库的创建，查询，更新和删除（CRUD）。

我们先把留言板的页面创建好，大概是这个样子：

![留言板](https://i.loli.net/2020/02/27/5CjNygDOhmEP7Yo.png)

index.html 代码如下，用到了 Bootstrap 的栅格，将用户名，邮箱地址和个人网站分成三栏：

```markup
{% extends "base.html" %}

{% block title %}
留言板
{% endblock %}

{% block body %}
<h1>留言板</h1>
<form method="post">
    {{ form.csrf_token }}
    <div class="row">
        <div class="col-sm">{{ form.username }}</div>
        <div class="col-sm">{{ form.email }}</div>
        <div class="col-sm">{{ form.website }}</div>
    </div>
    {{ form.message }}
    {{ form.submit }}
</form>
{% endblock %}
```

在 app.py 中创建表单类，并通过 `render_kw` 参数渲染属性。我们先把留言板的模板搭建好，然后再连接数据库。为了方便查看，我们将表单和视图函数放在同一文件中（真实开发中最好分开存放，方便后期统一管理和维护）。

```python
...
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField, SubmitField
from wtforms.validators import Email, DataRequired, URL, Optional

class MsgBoardForm(FlaskForm):
    username = StringField("用户名", validators=[DataRequired()],
                           render_kw={"class": "form-control",
                                      "placeholder": "昵称"})
    email = StringField("电子邮箱", validators=[Email(), DataRequired()],
                        render_kw={"class": "form-control",
                                   "placeholder": "电子邮箱"})
    website = StringField("个人网站", validators=[Optional(), URL()],
                          render_kw={"class": "form-control",
                                     "placeholder": "网址"})
    message = TextAreaField(validators=[DataRequired()],
                            render_kw={"class": "form-control msg-main",
                                       "placeholder": "说点什么吧"})
    submit = SubmitField("留言", render_kw={"class": "btn btn-primary"})

@app.route("/", methods=["GET", "POST"])
def index():
    form = MsgBoardForm()
    if form.validate_on_submit():
        username = form.username.data
        email = form.email.data
        website = form.website.data
        message = form.message.data
        return redirect(url_for("index"))
    return render_template("index.html", form=form)
```

# 配置数据库并定义模型

使用 SQLite 数据库，无需配置和管理，连接即可使用。

```python
from flask_sqlalchemy import SQLAlchemy

...
basedir = os.path.abspath(os.path.dirname(__file__))
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///" + os.path.join(basedir, "data.sqlite")
app.config["SQLALCHEMY_COMMIT_ON_TEARDOWN"] = True
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
db = SQLAlchemy(app)

class MsgBoard(db.Model):

    __tablename__ = "message_board"

    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String)
    email = db.Column(db.String)
    website = db.Column(db.String, nullable=True)
    message = db.Column(db.Text)

    def __repr__(self):
        return self.username
```

以下代码在创建数据库的时候会在项目根目录创建 data.sqlite 数据库文件。

```python
basedir = os.path.abspath(os.path.dirname(__file__))
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///" + os.path.join(basedir, "data.sqlite")
```

我们自定义的 `MsgBoard` 类通过继承 `db.Model` 与数据库连接。使用 `__tablename__` 自定义表名，使用 `db.Column` 创建数据表中的字段。

`db.Column` 的第一个参数是可选的，如果我们需要自定义字段名，就可以给该参数赋值，如果不填，默认使用变量名生成该字段名。例如：

```python
id = db.Column("msg_id", db.Integer, primary_key=True)
```

我们使用 `__repr__()` 方法，可以帮助我们返回一些更直接的信息，我们这里返回留言的用户昵称，之后在创建好数据实例的时候，只要输入实例名称，就会返回用户昵称。为了更方便的查看数据，这里也需要根据不同的项目做不同的修改。

# 测试数据库

## 创建记录

使用 flask shell 方便我们交互查看与操作数据库，[官方文档](https://docs.sqlalchemy.org/en/13/orm/tutorial.html)。

终端输入 `flask shell`，进入交互界面。

```bash
>>> from app import db, MsgBoard
>>> db
<SQLAlchemy engine=sqlite:////Users/Documents/FlaskGuide/data.sqlite>
>>> MsgBoard
<class 'app.MsgBoard'>
>>> msg1 = MsgBoard(username="测试用户名", email="email@qq.com", website="https://lijianxun.top/", message="测试测试")
>>> db.session.add(msg1)
>>> db.session.commit()
>>> msg1.id
1
>>> msg1.username
'测试用户名'
>>> msg1
测试用户名
```

创建一条数据分为三步：

1.  创建 Python 对象（`msg1 = MsgBoard(...)`）
2.  添加到会话中（`db.session.add(msg1)`）
3.  提交会话（`db.session.commit()`）

我们这里输入 `msg1` 返回 `测试用户名`，正是 `__repr__()` 方法起了作用，之后的查询我们还会看到该方法返回的数据。

## 查询记录

我们使用模型的 query 属性，配合过滤条件查询数据。

```bash
>>> # 查询 MsgBoard 的全部数据
>>> MsgBoard.query.all()
[测试用户名]
>>> # 查询 MsgBoard 的总量
>>> MsgBoard.query.count()
1
>>> # 通过主键查询
>>> MsgBoard.query.get(1)
测试用户名
>>> # 条件查询
>>> MsgBoard.query.filter_by(email="email@qq.com").all()
[测试用户名]
```

更多查询和过滤用法官方有更多介绍 [query](https://docs.sqlalchemy.org/en/13/orm/query.html) 和 [sqlelement](https://docs.sqlalchemy.org/en/13/core/sqlelement.html)。

## 更新记录

更新数据比较简单，只需要找到需要更新的项，重新赋值即可。

```bash
>>> msg = MsgBoard.query.get(1)
>>> msg
测试用户名
>>> msg.email
'email@qq.com'
>>> msg.email = "test@qq.com"
>>> msg.email
'test@qq.com'
>>> db.session.commit()
```

## 删除记录

删除记录和添加记录很类似，把 add 改成 delete 并提交即可。

```bash
>>> msg = MsgBoard.query.get(1)
>>> db.session.delete(msg)
>>> db.session.commit()
>>> >>> MsgBoard.query.all()
[]
```

# 留言板小项目

## 表单提交数据

在此之前我们先改动一下 `__repr__()` 方法，让它更好的展示数据。

```python
def __repr__(self):
    return "昵称：{}，电子邮箱：{}，网址：{}，留言内容：{}".format(
        self.username, self.email, self.website, self.message
    )
```

修改视图函数，将表单中的数据插入到数据库中。

```python
@app.route("/", methods=["GET", "POST"])
def index():
    form = MsgBoardForm()
    if form.validate_on_submit():
        username = form.username.data
        email = form.email.data
        website = form.website.data
        message = form.message.data
        msg = MsgBoard(
            username=username,
            email=email,
            website=website,
            message=message
        )
        db.session.add(msg)
        db.session.commit()
        print("插入成功")
        return redirect(url_for("index"))
    return render_template("index.html", form=form)
```

打开网页，我们插入一条数据，之后在 shell 中查询这条数据。

```bash
>>> MsgBoard.query.all()
[昵称：简讯，电子邮箱：i@lijianxun.top，网址：https://lijianxun.top，留言内容：这是一段留言。]
```

## 展示数据

插入完成后，我们需要把留言展示到页面上。我们需要修改视图函数，让数据展示到页面上。

```python
@app.route("/", methods=["GET", "POST"])
def index():
    form = MsgBoardForm()
    if form.validate_on_submit():
        username = form.username.data
        ...
        return redirect(url_for("index"))

    msg_board = MsgBoard.query.all()
    return render_template("index.html", form=form, msg_board=msg_board)
```

模版需要加入数据展示部分：

```markup
{% if msg_board | length < 1 %}
暂无留言
{% else %}
{% for msg in msg_board %}
<div class="shadow p-3 mb-5 bg-white rounded">
    {{ msg.website }}
    <div class="mb-0">
        {% if msg.website%}
        <a href="{{ msg.website }}">
            {{ msg.username }}
        </a>
        {% else %}
         {{ msg.username }}
        {%endif%}
    </div>
    <div class="blockquote">{{ msg.message }}</div>
</div>
{% endfor %}
{% endif %}
```

当无留言的时候会显示暂无留言，当有留言的时候才会展示到页面上。

![留言板展示](https://i.loli.net/2020/02/27/5uQbImAUpr4JOxs.png)

因为电子邮箱属于隐私，所以页面上不展示。如果填写了个人网址可以通过点击昵称跳转。

这只是一个非常简单的例子，如果真正需要做留言板，我们还需要加入更多字段，例如创建留言的时间戳，是否支持修改留言，回复留言（盖楼）等。

# Flask-MongoEngine

在 Flask 中使用 MongoDB，需要安装 Flask-MongoEngine。更详细的文档需要查看 [MongoEngine](http://docs.mongoengine.org/tutorial.html)。

## 安装

```bash
pipenv install flask-mongoengine
```

## 连接并展示数据

和使用 `Flask-SQLAlchemy` 大同小异：

```python
from flask_mongoengine import MongoEngine
import mongoengine as mongodb
...

app.config["MONGODB_SETTINGS"] = {
    "db": "msgBoard",
    "host": "localhost",
    "port": 27017
}

db = MongoEngine(app)

class MsgBoard(mongodb.Document):

    username = mongodb.StringField(required=True)
    email = mongodb.EmailField()
    website = mongodb.URLField()
    message = mongodb.StringField()

    def __repr__(self):
        return "昵称：{}，电子邮箱：{}，网址：{}，留言内容：{}".format(
            self.username, self.email, self.website, self.message
        )
```

我们同样可以使用 flask shell 测试数据库的连接，MongoEngine 默认字段都是选填的，不是必填字段都可以跳过。插入数据效果如下：

![插入数据](https://i.loli.net/2020/02/27/mwTK62JGiOkaQBl.png)

只需要简单修改视图函数，模板无需改动：

```python
@app.route("/", methods=["GET", "POST"])
def index():
    form = MsgBoardForm()
    if form.validate_on_submit():
        username = form.username.data
        email = form.email.data
        website = form.website.data
        message = form.message.data
        msg = MsgBoard(
            username=username,
            email=email,
            website=website,
            message=message
        )
        msg.save()
        return redirect(url_for("index"))

    msg_board = MsgBoard.objects.all()
    return render_template("index.html", form=form, msg_board=msg_board)
```

![mongo展示](https://i.loli.net/2020/02/27/apEMCyn7d6s59Y2.png)