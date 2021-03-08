---
title: Flask 框架使用自定义命令
tags:
  - Flask
id: '96'
categories:
  - - 编程学习
date: 2019-04-23 09:57:08
---

![](http://docs.jinkan.org/docs/flask/_images/logo-full.png) 在使用flask构建网页的时候，难免会遇到某一命令重复使用的情况。最典型的例子就是数据库的相关操作。在编码初期，如果操作数据库出错，最简单的办法就是删除重新生成一个新的数据库文件。但是反复生成或删除数据库的工作很无趣，有没有什么更简易的方法？官方提供了一个基于 `Click` 命令行接口的自定义命令的功能，用这个功能我们可以把经常用到的操作写成一个方法，直接调用就可以了。
<!-- more -->
为了练习自定义命令，我们简单创建几个文件。

```
.
├── .flaskenv
└── test
    ├── __init__.py
    └── commands.py
```

`.flaskenv`用来指明应用的名称为`test`。 **`__init__.py`文件：**

```python
from flask import Flask


app = Flask(__name__)

from test import commands
```

**`commands.py`文件：**

```python
import click
import time
import os

from test import app


@app.cli.command()
def mkdir():
    """
    生成一个以时间戳命名的文件夹
    """
    os.system("mkdir {}".format(time.time()))
    click.echo('生成完成！')
```

生成好以后我们在终端执行： `flask mkdir` 发现在与`.flaskenv`同目录下生成了一个以时间戳命名的文件夹。 对于自定义命令官方还有很多介绍：[自定义命令](https://dormousehole.readthedocs.io/en/latest/cli.html#id9)