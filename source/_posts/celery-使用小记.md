---
title: Celery 使用小记
tags:
  - Celery
  - Python
  - 任务队列
id: '88'
categories:
  - - 编程学习
date: 2019-08-29 04:48:16
---

![](https://cdn.pixabay.com/photo/2017/03/05/20/09/hurry-2119711__480.jpg) Celery 是一个简单高效可靠的分布式系统。在处理大量消息，实时处理异步任务，定时执行任务，支持任务调度等方面使用起来更为灵活。 简单理解 Celery 就是发布任务(Producer)，消息中间件(Broker)接收任务，执行任务(Worker)或定时执行任务(Celery Beat)。
<!-- more -->
# 任务队列

任务队列，顾名思义就是任务工作单元的集合，集合中存放了等待执行的任务。 何时使用任务队列？假如当前我们需要执行某项任务，但是该任务比较耗时，且可以放在后台执行，为了不影响当前操作，我们就把该任务放在任务队列，等待调用和执行。最常使用的场景是在 web 开发中。例如我们需要实现一个用户认证发送电子邮件的功能，如果不使用异步执行，我们需要等待电子邮件发送完成才可执行下步操作，但是发送邮件的过程中，web 页面会处于假死状态，影响用户使用体验。简单的 web 应用可以用多线程来完成该任务，一旦任务量加重，还是需要使用更为强大的 Celery。 定时执行任务，可以理解为 crontab。

# 安装

使用 Celery 的第一步就是选择消息中间件。这里我们选择 redis，上手快好操作。

## 安装 redis

[官方文档](https://redis.io/download#installation)有详细的源码安装说明。 这里介绍在 Ubuntu 16.04 下通过 apt 工具安装： `apt-get install redis-server -y` 启动 redis： `redis-server` 测试是否成功安装：

```bash
➜  ~ redis-cli
127.0.0.1:6379> PING
PONG
```

说明安装成功。 终端输入： `python3 -m pip install redis` 这样我们可以使用 python 操作 redis。

## 安装 Celery

终端执行： `python3 -m pip install celery` 这样就完成了 redis 和 Celery 的安装。

# 简单应用

当我们安装好 redis，并启动服务后，可以创建一个新的文件夹用来练习 celery。 `mkdir celery_test` 进入该目录后，创建文件 `test.py`，输入以下内容：

```python
from celery import Celery


app = Celery(
    'hello',
    broker="redis://127.0.0.1:6379/1"
)

@app.task
def hello():
    # 耗时需要异步执行的任务
    return 'hello world'
```

这就完成了 Celery 的简单应用。 如何使用呢？ 我们需要在终端启动 Celery 服务。输入： `celery worker -A test --loglevel INFO` 如果看到以下界面说明成功启动！ ![celery 启动界面](https://i.loli.net/2019/08/29/9nZkF73uJPfGh6v.png) 使用 python shell 简单试一下：

```bash
>>> from test import hello
>>> hello.delay()
<AsyncResult: 182f57d7-71fc-4c61-b6b4-dbdd76ac4068>
```

然后查看 Celery，可以看到最下方有一个新的 Worker 被执行： ![任务被执行](https://i.loli.net/2019/08/29/dAPzc2F6bHSGkvj.png) 是不是很简单？这样就完成了 Celery 的简单使用。

# 使用配置文件

我们可以将 Celery 任务单独做成模块的形式，并通过配置文件来配置 Celery。 结构如下：

```bash
.
├── app
│   ├── __init__.py
│   ├── config.py
│   └── tasks.py
└── test.py
```

`app` 文件夹下存放 Celery 相关配置和任务。 `__init__.py` 用来初始化 Celery，`config.py` 是 Celery 的配置文件，`tasks.py` 存放任务。 `test.py` 用来测试执行任务。

## **init**.py

```python
from celery import Celery


app = Celery("hello")

# 使用配置文件
app.config_from_object("app.config")
```

## config.py

```python
"""
Celery 配置
"""

broker_url = "redis://127.0.0.1:6379/1"
```

很多教程基本使用都是大写配置，但官方文档中介绍在 4.0 以后开始推荐使用小写配置，所以依据文档这里使用小写配置。 具体更多详细配置可以查看[Configuration and defaults](http://docs.celeryproject.org/en/latest/userguide/configuration.html#new-lowercase-settings)。

## tasks.py

```python
from app import app


@app.task
def hello():
    return "hello, world!"
```

这个文件存放耗时任务。 在终端启动 Celery： `celery worker -A app.tasks --loglevel INFO` 启动成功后在 python shell 中测试：

```bash
>>> from app import tasks
>>> tasks.hello.delay()
<AsyncResult: 3aaa9e64-66ea-42dc-bed7-b1921275e9f9>
```

查看 Celery，和之前一样，说明就执行成功啦！

# 部署 Celery 和 redis

我们需要使用 supervisor 启动和管理 Celery 和 redis 服务。 `pip install supervisor` 安装完成后修改配置文件 `/etc/supervisord.conf`，加入以下配置（redis 的配置要写在 Celery 之前）：

```config
[program:redis]
user = root
directory=/root/celery_test
command=redis-server
numprocs=1
startsecs = 5
startretries = 3
autostart=true
autorestart=true
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile = /root/logs/redis_stdout.log

[program:celery_worker]
user = root
directory=/root/celery_test
command=celery worker -A app.tasks --loglevel INFO --logfile /root/celery_worker.log
numprocs=1
startsecs = 5
startretries = 3
autostart=true
autorestart=true
stdout_logfile = /root/logs/celery_stdout.log
```

输入命令启动服务： `supervisord -c /etc/supervisord.conf` 这样就完成了 redis 和 Celery 的部署。