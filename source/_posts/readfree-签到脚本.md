---
title: Readfree 签到脚本
tags:
  - Python
  - 脚本
id: '108'
categories:
  - - 贵在折腾
date: 2019-01-14 12:11:10
---

![](https://cdn.pixabay.com/photo/2016/09/21/16/11/hacking-1685092__480.jpg) [readfree](https://readfree.me/)是一个电子书分享网站, 不过下载电子书需要通过签到来获取额度.每天签到的事情可以交给定时脚本完成.
<!-- more -->
## 获取Cookie

使用抓包工具或者Chrome开发者工具, 获取当前账号的cookie. cookie格式类似:

```
Cookie: Hm_lvt_375aa6d601368176e50751c1c6bf0e82=1548247586; Hm_lpvt_375aa6d601368176e50751c1c6bf0e82=1548247586; csrftoken=rqvTsfmbZWXKiSZ75naakfO1uivezyKnRN2dtfUJUcq0enlrLNW0CtleLjxKZwzf; sessionid=61iyroy6x37oqwuqs2cr032jr0bupnqj
```

## 签到脚本

获取到你的cookie后, 直接复制这段脚本, 运行即可签到.

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
Desc:

    readfree签到脚本

"""

import urllib2
import time
import random


url = "http://readfree.me/accounts/checkin"

headers = {
    "Cookie": "Hm_lvt_375aa6q601368176e50751c1c6bf0e82=1547034261,1547364073; Hm_lpvt_375aa6d601368176e50751c1c6bf0e82=1542364556; sessionid=ec99pxnfjpf5qx2a7yxunskw3o0l440z; csrftoken=URVYo37WiTOteIzHN1r4SZ47GUFaUAiCphk7VZrBVu3n8OPg2Bi7aONjqTfnAPz8",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36"
}

sleep_time = random.choice(range(1, 10))
time.sleep(sleep_time)
request = urllib2.Request(url, headers=headers)
response = urllib2.urlopen(request)
response.read()
```

加了随机睡眠时间是防止每次签到都是在同一时刻, 其实可加可不加. **将该脚本保存在`/usr/games/`路径下。**

## 定时执行

定时执行需要用到`crontab`命令.

> crontab命令常见于Unix和类Unix的操作系统之中，用于设置周期性被执行的指令。(维基百科)

终端输入: `crontab -e` 第一次进入会提示你使用什么编辑器, 可选择vim。 在最后一行输入: `30 06 * * * python /usr/games/readfree.py` 这样每天早上的六点三十分就会执行该命令。