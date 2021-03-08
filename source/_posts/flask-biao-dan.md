---
title: Flask 表单
tags:
  - Flask
  - 表单
id: '215'
categories:
  - - Flask 教程
date: 2020-02-20 14:07:40
---

说起 HTML 表单，相信大家都不陌生，只要浏览网页，多多少少一定会使用到表单功能。比如说登录，留言，撰写文章等，有输入和提交的地方就有表单。这么常用的功能，已经有强大的 [WTForms](https://wtforms.readthedocs.io/en/stable/index.html) 来帮助我们完成这项工作，和之前一样，Flask 同样有插件已经集成了 WTForms，并且在此基础上，增加了跨站请求伪造保护（CSRF），预防 SQL 注入攻击，文件上传和 reCAPTCHA 等，这款插件就是 [Flask-WTF](https://flask-wtf.readthedocs.io/en/stable/)，让我们在表单处理方面更愉悦。
<!-- more -->
# Flask-WTF

![登录页面](https://i.loli.net/2020/02/17/6TMFPdAhrLcQnlE.png)

如图，这就是一个简单的表单，可以实现登录和记住用户的功能。

我们需要做的事是使用 Flask-WTF 把这部分 HTML 表示出来，并传递到模板中。

# 安装

```bash
pipenv install flask-wtf
```

# 跨站请求伪造保护

跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去运行。这利用了 web 中用户身份验证的一个漏洞：简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。（[维基百科](https://zh.wikipedia.org/zh-cn/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0)）

我们需要配置一个密钥来实现 CSRF。这个密钥就是一个复杂的字符串，如果你的电脑里有密码管理软件，那么非常方便，随机生成一个就可以，例如这样：

```bash
k{/p9qw9vk4[x79XeZ7c8(q,y,g[Vs
```

设置密钥：

```python
app.secret_key = "k{/p9qw9vk4[x79XeZ7c8(q,y,g[Vs"
```

如果把密钥写入环境变量，然后读取使用会更加安全。

# 创建表单

app.py 代码，创建表单实例：

```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap
from flask_wtf import FlaskForm
from wtforms import (StringField, PasswordField, 
                     BooleanField, SubmitField)

app = Flask(__name__)
bootstrap = Bootstrap(app)
app.secret_key = "k{/p9qw9vk4[x79XeZ7c8(q,y,g[Vs"

class LoginForm(FlaskForm):
    username = StringField("用户名")
    password = PasswordField("密码")
    remember_me = BooleanField("记住我")
    submit = SubmitField("登录")

@app.route("/")
def index():
    login_form = LoginForm()
    return render_template("index.html", login_form=login_form)
```

这里我们用到了 StringField，PasswordField，BooleanField，SubmitField 四种字段类型，分别是文本字段，密码字段，复选框和提交按钮。[更多字段类型可以参考此文档](https://wtforms.readthedocs.io/en/stable/fields.html)。

index.html 代码，渲染 login\_form 表单：

```markup
{% extends "base.html" %}

{% block title %}
首页
{% endblock %}

{% block body %}
{{ login_form.csrf_token }}
<p>
    {{ login_form.username.label }}
    {{ login_form.username }}
</p>
<p>
    {{ login_form.password.label }}
    {{ login_form.password }}
</p>
<p>
    {{ login_form.remember_me.label }}
    {{ login_form.remember_me }}
</p>
<p>
    {{ login_form.submit }}
</p>
{% endblock %}
```

**记得写入隐藏字段：`{{ login_form.csrf_token }}`**

这个不会在页面上显示，但是正是因为这行代码让 CSRF 生效。

运行效果如下：

![表单渲染](https://i.loli.net/2020/02/19/AFyxQKNMaEGgeU2.png)

但是看起来很不美观，我们已经集成了 Bootstrap，该如何使用呢？

使用表单字段的 `render_kw` 属性来生成 HTML 属性。修改 app.py 中表单部分的代码：

```python
class LoginForm(FlaskForm):
    username = StringField("用户名", render_kw={"class": "form-control", "placeholder": "请输入用户名"})
    password = PasswordField("密码", render_kw={"class": "form-control", "placeholder": "请输入密码"})
    remember_me = BooleanField("记住我")
    submit = SubmitField("登录", render_kw={"class": "btn btn-primary"})
```

效果如下：

![使用bs](https://i.loli.net/2020/02/19/Jq8Y2tnL3VCEADI.png)

是不是一下美观了许多。

可能其他教程会提供第二种添加属性的办法，是在模板中操作的：

```markup
{{ login_form.username(class="form-control", placeholder="请输入用户名") }}
```

但是我个人并不推荐这种写法。我认为模板的功能是接受参数并渲染页面，使用少量过滤器即可。虽然标签属性属于 HTML，但是能让表单类处理的最好不要交给模板。而且交给表单类可以让代码很直观，并不复杂。不仅好维护，看起来也非常顺眼。

# 验证器

用户填写完表单，我们需要做表单校验工作，这里就用到了验证器。例如我们平时使用表单的时候，会遇到只能填入数字，只能填入英文字母和数字，字段是否是必填项，有的需要限制数据长度，数据格式等等，都需要用到验证器。

使用 WTForms 提供的验证器，可以让我们不用考虑如何编写 HTMl 属性。

我们需要导入验证器，作为字段参数使用。

```python
from wtforms.validators import DataRequired

username = StringField("用户名", validators=[DataRequired()])
```

例如这部分代码就是引入必填字段验证器并使用。

在 HTML 中是这样的：

```markup
<input class="form-control" id="username" name="username" placeholder="请输入密码" type="text" value="" required>
```

如果在提交的时候这个字段没有数据，就会提示字段不能为空。

常用的验证器：

验证器

说明

Email

验证电子邮件地址

EqualTo

比较两字段的值是否相同，例如注册时二次输入密码

Length(min=-1, max=-1)

判断输入字段的长度

NumberRange

判断输入数字的范围

Required

确保字段中有数据

URL

验证 URL

Regexp

使用正则表达式验证输入值

如果没有合适的验证器，我们还可以自定义验证器。

```python
from wtforms.validators import ValidationError

def url_is_https(form, field):
    """
    验证 URL 是否 https 开头
    """
    if field.startswith("https"):
        return ValidationError("URL 必须使用 https！")
```

使用起来和内置验证器一样。

# 处理错误消息

首先我们需要更新代码来实现错误消息的展示。将登录信息改为电子邮箱登录。

```python
from flask import Flask, render_template, flash
from flask_bootstrap import Bootstrap
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import Email, DataRequired

app = Flask(__name__)
bootstrap = Bootstrap(app)
app.secret_key = "k{/p9qw9vk4[x79XeZ7c8(q,y,g[Vs"

class LoginForm(FlaskForm):
    username = StringField("电子邮箱", render_kw={"class": "form-control", "placeholder": "请输入电子邮箱"},
                           validators=[Email(), DataRequired()])
    password = PasswordField("密码", render_kw={"class": "form-control", "placeholder": "请输入密码"},
                             validators=[DataRequired()])
    remember_me = BooleanField("记住我")
    submit = SubmitField("登录", render_kw={"class": "btn btn-primary"})

@app.route("/", methods=["GET", "POST"])
def index():
    login_form = LoginForm()
    if login_form.validate_on_submit():
        flash("表单提交成功!")
    return render_template("index.html", login_form=login_form)
```

index.html 增加了内置的消息提示和错误提示：

```markup
{% extends "base.html" %}

{% block title %}
首页
{% endblock %}

{% block body %}

{% for msg in get_flashed_messages() %}
<div class="alert alert-success" role="alert">
    {{ msg }}
</div>
{% endfor %}

<form method="post">
    {{ login_form.csrf_token }}
    <p>
        {{ login_form.username.label }}
        {{ login_form.username }}

        {% for err in login_form.username.errors %}
            <small class="text-danger">{{ err }}</small>
        {% endfor %}
    </p>
    <p>
        {{ login_form.password.label }}
        {{ login_form.password }}
    </p>
    <p>
        {{ login_form.remember_me.label }}
        {{ login_form.remember_me }}
    </p>
    <p>
        {{ login_form.submit }}
    </p>
</form>
{% endblock %}
```

这样在我们没有填写 Email 或者格式不正确就会看到错误提示：

![错误提示](https://i.loli.net/2020/02/19/EeocPBAUjQDKRfZ.png)

会看到提示**Invalid email address.**。只不过是英文的，怎么改成中文的呢？

需要改动以下部分代码：

```python
app.config["WTF_I18N_ENABLED"] = False

class BaseForm(FlaskForm):

    class Meta:
        locales = ["zh"]

class LoginForm(BaseForm):
    ...
```

我们需要编写基类继承 `FlaskForm`，定义 `Meta` 类，然后在表单组件继承我们创建的基类。

![错误显示中文](https://i.loli.net/2020/02/19/OsSRxoGWk49XKMq.png)

可以看到已经变成中文了。

如果我们每个标签都要处理错误消息，会产生大量重复的代码。还记得我们之前说过的宏吗？可以使用宏大幅减少代码量。

编写宏：

```markup
{% macro form_data(form_field) %}
<p>
    {{ form_field.label }}
    {{ form_field }}
    {% for err in form_field.errors %}
    <small class="text-danger">{{ err }}</small>
    {% endfor %}
</p>
{% endmacro %}
```

使用宏替代之前的电子邮件和密码字段的部分：

```markup
{{ macros.form_data(login_form.username) }}
{{ macros.form_data(login_form.password) }}
```

运行以后可以看到效果是一样的，但是帮我们节省了很多事情。

# 重新发送表单问题

![重定向](https://i.loli.net/2020/02/19/e3RC7xFTM24qY1y.png)

相信我们都看过类似的提示框，之所以会出现这样的情况，是因为我们在刷新页面的时候会再次使用 POST 方式提交表单，导致浏览器需要用户确认。但一般情况我们刷新页面并不会再提交表单。这样就导致用户体验很不好。如何改善这个问题？

只要在刷新的时候不提交 POST 请求就可以了，也就是让最后一个请求使用 GET 方式。

```python
...
from flask import redirect, url_for

@app.route("/", methods=["GET", "POST"])
def index():
    login_form = LoginForm()
    if login_form.validate_on_submit():
        flash("表单提交成功!")
        return redirect(url_for("index"))
    return render_template("index.html", login_form=login_form)
```

我们需要引入 `redirect`, `url_for` 来完成重定向。

这样做就保证在成功提交表单以后，重定向到 `index` 对应的页面。也就避免了之前提到的问题。

# 文件上传

有关表单的话题离不开文件上传。

首先我们需要给 form 标签添加 `enctype=multipart/form-data` 属性。

app.py 代码：

```python
...
from wtforms import SubmitField, FileField

app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024
app.config['UPLOAD_FOLDER'] = '/path/to/the/uploads'

class FileForm(BaseForm):
    my_file = FileField("文件上传")
    submit = SubmitField("上传")
```

通过 `MAX_CONTENT_LENGTH` 字段控制上传文件的大小，`UPLOAD_FOLDER` 控制上传文件的路径。

index.html 代码：

```markup
{% extends "base.html" %}
{% import "macros.html" as macros %}

{% block title %}
上传文件
{% endblock %}

{% block body %}
<form method="post" enctype="multipart/form-data">
    {{ login_form.csrf_token }}
    {{ login_form.my_file }}
    {{ login_form.submit }}
</form>
{% endblock %}
```

![上传文件](https://i.loli.net/2020/02/20/SyVsuYm1z4HoRtv.png)

这里只是简单的实现的文件上传，视图函数还需要做很多工作。例如上传文件的名称必须修改成安全字符，上传文件类型的判断等等都需要在视图函数中处理。[详情查看官方文档。](https://flask.palletsprojects.com/en/1.1.x/patterns/fileuploads/)