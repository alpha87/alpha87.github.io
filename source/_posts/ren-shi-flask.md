---
title: 认识 Flask
tags:
  - Flask
  - flask-script
id: '189'
categories:
  - - Flask 教程
date: 2020-02-14 16:58:31
---

[Flask](https://github.com/pallets/flask) 是一个轻量级的 WSGI Web 应用程序框架。它旨在使快速入门 Web 开发变得容易，并具有扩展到复杂应用程序的能力。它最初是围绕 Werkzeug 和 Jinja 的简单包装，现在已成为最受欢迎的 Python Web 应用程序框架之一。
<!-- more -->
# 什么是 WSGI

WSGI 的全名是 Web Server Gateway Interface，Web 服务器网络接口，是为 Python 语言定义的 Web 服务器和 Web 应用程序或框架之间的一种简单而通用的接口。重点在**通用**。简单理解就是 Python 的 Web 框架有很多，需要搭建一个通用网关，这样不论什么框架写的 Web 程序都能统一运行。毕竟统一和规范才能进步。

其实这个简单了解一下就行，因为在实际开发中不会感受到它的存在，想深入的同学可以多了解。

# 安装

Flask 主要由两大部分组成：[Werkzeug](https://github.com/pallets/werkzeug) 和 [Jinja](https://github.com/pallets/jinja)。前者用来管理 Flask 的路由，调试，WSGI，后者就是 Flask 的模板系统。

可以使用 pipenv 创建新的虚拟环境，因为我的电脑里 Python3 的版本默认为 Python 3.8，所以在创建 Python3 环境的时候也会默认选择 Python 3.8 来搭建虚拟环境。不过只要是 Python 3.6 以上版本就基本差不多。

如果没有使用虚拟环境也不影响，安装命令：

```bash
python3 -m pip install -U flask
```

这条命令保证使用 Python3 的 pip 包来安装 Flask，并且如果你之前已经安装了 Flask，那么这次安装就会更新。

如果是虚拟环境，安装命令：

```bash
pipenv install flask
```

# 第一个 Flask 应用

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
```

## 运行

使用终端，在当前目录执行 `flask run`。

```bash
(FlaskGuide) ➜  FlaskGuide flask run
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

127.0.0.1 - - [13/Feb/2020 02:21:09] "GET / HTTP/1.1" 200 -
```

在本地打开 `http://127.0.0.1:5000/`，使用五行代码完成了第一个 Web 应用。几乎所有的 Flask 应用中，`app` 总是等于 `Flask(__name__)`。这是因为 Flask 类的构造函数有一个必须指定的参数，就是程序主模块或者包的名字，一般程序中，程序的主模块或者包的名字就是本身程序的名称，也就是 `__name__`。

可能在之前的一些教程中，大部分都会有如下代码：

```python
if __name__ == "__main__":
    app.run()
```

其实现在的版本也可以这样执行，但是不被建议。而且最好添加环境变量，如果 Flask 应用默认是 `app.py`，可以直接执行 `flask run`，如果是其他名称，就一定需要明确。例如新建了一个名为 `hello.py` 的应用，那么就要在环境变量中指明：

```bash
export flask_APP=hello.py
```

指明之后就可以使用 `flask run` 命令开启服务了。

## 调试模式

之前调试起来相比之下比较方便：

```python
if __name__ == "__main__":
    app.run(debug=True)
```

现在同样需要在环境变量里明确。

```bash
export FLASK_ENV=development
```

这样在启动就是调试模式，在改动代码之后会自动重启服务。

```bash
(FlaskGuide) ➜  FlaskGuide flask run
 * Environment: development
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 169-605-104
```

## 局域网访问

```bash
flask run --host=0.0.0.0
```

这样运行会监听所有公共 IP，只要在其他设备输入本地局域网 IP 就可以访问了。

## 使用 flask-script

当然了，如果你还是习惯使用 `app.run()`，可以尝试使用 `flask-script` 库。

`flask-script` 是 Flask 的拓展，为 Flask Web 程序添加了命令行解释器，还能自定义命令。

```python
from flask import Flask
from flask_script import Manager

app = Flask(__name__)
manager = Manager(app)

@app.route("/")
def index():
    return "hello, world."

if __name__ == '__main__':
    manager.run()
```

在终端直接运行 `python3 app.py`，会提示你少参数，也会提示一些常用参数。

```bash
(FlaskGuide) ➜  FlaskGuide python app.py          
usage: app.py [-?] {shell,runserver} ...

positional arguments:
  {shell,runserver}
    shell            Runs a Python shell inside Flask application context.
    runserver        Runs the Flask development server i.e. app.run()

optional arguments:
  -?, --help         show this help message and exit

```

如果想运行程序，执行 `python app.py runserver` 命令。

### 自定义命令

通过自定义命令会帮助我们解决许多重复工作。先简单举个例子：

```python
from flask import Flask
from flask_script import Manager, Command

app = Flask(__name__)
manager = Manager(app)

class Hello(Command):

    def run(self):
        print("自定义命令测试")

if __name__ == '__main__':
    manager.add_command('hello', Hello())
    manager.run()
```

终端运行 `python3 app.py hello` 就会执行自定义命令的 `run()` 方法了：

```bash
(FlaskGuide) ➜  FlaskGuide python app.py hello
自定义命令测试
```

其他使用方法还需查看[官方文档](https://flask-script.readthedocs.io/en/latest/)，非常详细。

# 包含动态路由的 Web 应用

```python
@app.route('/user')
def hello_stranger():
    return 'Hello, Stranger!'

@app.route('/user/<username>')
def hello_user(username):
    return f'Hello, {username}!'
```

`username` 就是通过 URL 动态获取的。

动态路由支持 int、float、string、 path 和 uuid 五种类型。前两种为数字，后三种为字符串，path 是可以包含斜杠的字符串。

转换器类型

说明

string

（默认）接受不带斜杠的任何文本

int

接受正整数

float

接受正浮点值

path

喜欢 string 但也接受斜线

uuid

接受 UUID 字符串

因为动态路由支持不同类型，所以我们可以传入不同的值来执行不同路由的任务。

```python
@app.route("/<int:parameter>")
def parameter_int(parameter):
    return "传入参数类型 Int"

@app.route("/<float:parameter>")
def parameter_float(parameter):
    return "传入参数类型 Float"

@app.route("/<parameter>")
def parameter_string(parameter):
    return "传入参数类型 String"
```

# 使用 `url_for()` 反向构建 URL

把 url 硬编码到模板中这种事情还是不要做得好，Flask 已经提供了映射方法。

```python
from flask import Flask, url_for
from markupsafe import escape

app = Flask(__name__)

@app.route('/')
def index():
    return 'index'

with app.test_request_context():
    print(url_for('index'))
```

可能第一次看到这种写法有点蒙圈，其实就是使用函数名获取路径名。

通过 `index` 找到对应节点 `/`。

也可以用这种方法导入静态文件：

```python
url_for('static', filename='style.css')
```

对应静态文件路径：

```bash
static/style.css
```