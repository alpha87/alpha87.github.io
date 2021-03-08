---
title: 使用 pytest 来测试你的代码
tags:
  - pytest
  - Python
id: '54'
categories:
  - - 编程学习
date: 2019-09-15 10:41:44
---

![](https://cdn.pixabay.com/photo/2015/01/12/03/25/keyboard-597007__480.jpg)

> 对于任何软件质量规划来说，测试都是一个重要的组成部分，并且在许多情况下它是唯一的组成部分。 ——《代码大全》

可见测试在整个项目开发过程中的地位是非常重要的。优秀的开源项目往往都具备完善的测试代码。
<!-- more -->
# 为什么要测试

我们这里说的测试，一般都是指“单元测试”。 编写测试有许多好处。 最基本的功能就是提升代码质量，在代码上线前及时发现异常，使项目更加健壮。实现新功能的时候，可以保证代码按照预期执行。而且我们修改代码或修复 bug 后，运行测试可以保证我们代码功能没有退化，不会因为修复某一异常导致另一异常的产生。间接减少了调试的时间，让开发人员对项目更有信心。 《python 网络数据采集》书中提到单元测试通常包含四个特点：

1.  每个单元测试用于测试一个组件。
2.  每个单元测试都可以完全独立的运行。
3.  每个单元测试通常至少包含一个断言（但是最好只有一个）。
4.  单元测试与生产代码是分离的。

第一个和第二个特点同时说明编写单元测试，也是在考验代码的编写质量。如果在编写测试的时候，发现无从下手，或各种函数依赖过多，可能是函数过于复杂，并没有做到一个函数处理一个问题。这时就应该及时修改代码，使其更加简洁。测试代码中断言是必须的，断言可以保证代码执行结果是否符合我们预期，符合预期才可通过测试。 但是对于大部分初学者来说，可能认为测试离自己很遥远。一方面是代码功能都不一定能实现，就别说测试了；另一方面是不会编写测试代码，且对整个开发过程没有明显影响，久而久之就淡忘了编写代码过程中其实还有编写测试代码这一环节。针对这两种想法，我认为代码功能迟早会实现，编码过程不要心急，无法实现某项功能，和是否编写测试代码并无关系，编写测试代码的习惯一定要养成；对于不会编写测试代码的同学，可以看看优秀的开源项目，也可以尽量为自己的项目编写测试代码，可能代码非常简单，简单到你认为无需编写测试代码，但也要坚持编写。在《python web开发 测试驱动方法》书中第一章，作者在未启动 Django 服务情况下，仍然使用断言 `assert 'Django' in browser.title`。当时我看到这部分认为这一举动是非常可笑的，有点为了测试而测试，因为谁会在没启动 Django 服务的时候测试代码呢？但这正是测试驱动开发巧妙的地方，先编写测试，再编写代码。只要通过了测试，就说明代码执行正确。保证了代码的简洁。

# python 单元测试

python 有许多测试框架，这里推荐使用 pytest。 pytest 可以编写小型测试，也可拓展编写更复杂的功能测试。有完善的文档，丰富的命令和各种插件支持，使测试更加灵活简单。

## 安装 pytest

`python3 -m pip install pytest`

## 第一个测试

安装完成后，简单运行一个测试。

```python
import pytest


def func(n: int):
    return n + 1


def test_func():
    assert func(1) == 3
```

![第一个测试](https://i.loli.net/2019/09/15/ejSrFGkJhaXgicd.png) 当然这个明显会报错，但是我们可以看出，编写测试代码比较简单，无需任何配置。 pytest 会自动发现我们需要测试的代码，这取决于 pytest 发现测试代码的约定。

## 测试异常是否能被捕获

pytest 可以测试代码是否按预期执行，也可以测试异常是否能被正确捕获。

```python
import pytest


def f():
    raise SystemExit(1)


def test_mytest():
    with pytest.raises(SystemExit):
        f()
```

测试结果符合预期。 ![捕获异常测试](https://i.loli.net/2019/09/15/DG6xl9iXW1Ee7gC.png) 这样我们在项目中使用异常或自定义异常时，可以使用该方法测试异常是否能正确被捕捉。

## 发现测试代码的约定

如果没有附加参数，pytest 会遵循递归目录的方式，找到以 `test_` 开头的文件夹，`test_*.py` 或 `*_test.py` 的文件，在这些文件中找到 `test` 前缀的方法或以 `Test` 开头命名的类（不包含 `__init__` 方法）。 当然我们写了许多测试，不想一次测试所有代码，pytest 也支持选择性测试。

### 在模块中运行测试

`pytest test_mod.py`

### 在目录中运行测试

`pytest testing/`

### 按关键字表达式运行测试

`pytest -k "MyClass and not method"` 运行给定字符串表达式匹配的测试。如上命令意思是只测试命名中包含 `MyClass` 且不含 `method` 字符串的测试。命名范围包括类名和函数名。 举个例子：

```python
import pytest


class TestMyClass(object):

    def test_func(self):
        assert 1 == 1

    def test_method_func(self):
        assert 1 != 1
```

这段代码中，若配合上述命令，只会执行 `TestMyClass` 下的 `test_func` 测试。如果我们将类名从 `TestMyClass` 改成 `TestYourClass`，将不会执行任何测试。因为我们的测试命名中，关键字既没有 `TestYourClass` 也不能包含 `method`。

### 按节点 ID 运行测试

每个收集的测试都分配一个唯一的 nodeid，包括模块文件名，类名，函数名和参数，用`::`字符分隔。 在模块中运行特定测试： `pytest test_mod.py::test_func` 在命令行中指定测试方法的示例： `pytest test_mod.py::TestMyClass::test_method_func`

## Fixtures 和 conftest.py

Fixtures 是简单理解是一个在测试之前运行的函数。可以将一些必要的数据提供给测试。例如连接数据库，爬虫请求返回网页内容和某种数据等。 简单举例：

```python
import re

import pytest
import requests


@pytest.fixture
def title():
    """
    使用 requests 请求 https://lijianxun.top/
    返回网站 title
    """

    html = requests.get("https://lijianxun.top")
    doc = html.text
    title = re.findall("<title>(.*?)</title>", doc)[0]
    return title


def test_add(title: str):
    assert "简讯" in title
```

运行测试，返回如下结果： ![fixture测试1](https://i.loli.net/2019/09/17/V57RfaYjEdhG41i.png) 我们并没有在其他地方传入 `title`，pytest 可自动匹配。 如果我们需要的测试数据过多，可以单独创建一个文件 `conftest.py` 来统一管理 Fixtures。 `conftest.py` 文件写入：

```python
import re
import pytest
import requests

@pytest.fixture
def title():
    """
    使用 requests 请求 https://lijianxun.top/
    返回 title
    """

    html = requests.get("https://lijianxun.top")
    doc = html.text
    title = re.findall("<title>(.*?)</title>", doc)[0]
    return title
```

`test_title.py` 写入：

```python
def test_add(title: str):
    assert "简讯" in title
```

这样也是可以的。 命令行使用参数 `--fixtures` 可以查看所有可用 Fixtures。 命令行输入： `pytest --fixtures conftest.py` 会返回：

```bash
title
    使用 requests 请求 https://lijianxun.top/
    返回 title
```

所以在编写 Fixtures 测试数据时，函数最好添加文档字符串，保证在之后查看明确各种函数的用处。

# 如何编写测试代码

介绍了 pytest 的用法，说到底还是为了编写测试代码服务的，但是如何编写测试代码呢？ 测试并不复杂。明确需要测试的部分，声明预期的结果，符合预期便可通过测试。单元测试要测试的其实是逻辑、流程控制和配置。毕竟测试代码是死的，程序是活的。因为测试代码一旦写好，说明程序必须按照该测试执行，这样就可以放心修改代码，增加功能或重构。 例如写一个爬虫，数据是以字典的形式入库，其中有一些字段在源网站是 int 类型，因为业务需要，我要保证存储下来的是 str 类型，而且之后可能还会新增字段，也需要保证转换成 str 类型，这里可以写一个测试来判断入库的字段是否全部都是 str 类型。可能有读者会说，写测试这点功夫直接转换类型不就行了吗？但是谁能保证在之后修改代码的时候，重构或者有其他人接手这部分代码的时候还会想起转换类型呢？ 正如《python web开发 测试驱动方法》书中说到，写测试代码就像写故事一样，把步骤写下来就组成了一个故事。比如用 Flask 搭建博客，我们需要测试用户登录，发帖，提交，返回首页博文展示到首页等一系列动作，故事写完了我们需要测试的部分也就想出来了。

# 最后

编写测试代码可能是枯燥的，但测试是开发过程中一个重要的组成部分。良好的测试可以保证程序运行的稳定性。所以为了你的代码，从今天开始学习写测试代码吧！