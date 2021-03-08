---
title: 记录 Django 时间处理遇到的坑
tags:
  - Django
  - Python
id: '35'
categories:
  - - 编程学习
date: 2019-05-14 10:05:00
---

![](https://i.loli.net/2020/02/06/yUBWREsLAOYSwcQ.jpg) Django新项目生成的时候，我们首先需要更改默认的时区设置。有以下两点需要注意。
<!-- more -->
以下代码片段来自Django项目生成后的`settings.py`文件。

```python
LANGUAGE_CODE = 'en'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True
```

一般情况下，多数教程都会告诉我们修改`LANGUAGE_CODE`和`TIME_ZONE`，改为：

```python
LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True

USE_TZ = True
```

开启服务，界面和时间的确没问题。 但是当我们有入库需求的时候，且字段正好需要显示完整的时间（包括时分秒）。就会发现入库的时间还是`UTC`时间。 查看[官方文档](https://docs.djangoproject.com/zh-hans/2.1/topics/i18n/timezones/)介绍时区的部分，默认禁用时区支持是避免夏令时转换时造成时间上的错误。

> Even if your website is available in only one time zone, it's still good practice to store data in UTC in your database. The main reason is Daylight Saving Time (DST). Many countries have a system of DST, where clocks are moved forward in spring and backward in autumn. If you're working in local time, you're likely to encounter errors twice a year, when the transitions happen. (The pytz documentation discusses these issues in greater detail.) This probably doesn't matter for your blog, but it's a problem if you over-bill or under-bill your customers by one hour, twice a year, every year. The solution to this problem is to use UTC in the code and use local time only when interacting with end users. Time zone support is disabled by default.

但是如果你确定你的程序不存在这些问题，入库或其他操作需要显示当地时间，那么就改为**`USE_TZ = False`**，这样入库时间就是当时的时间了。