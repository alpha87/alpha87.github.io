---
title: 使用 Python 操作 Telegram 发送消息
tags:
  - Python
  - Telegram
id: '55'
categories:
  - - 贵在折腾
date: 2020-02-01 20:42:56
---

![](https://i.loli.net/2020/02/06/9zr38v7JpwfDASd.png)

用过 tg 的朋友一定认同 tg 是宇宙第一通讯工具，没记错的话应该是战斗民族俄罗斯的程序员开发的。
<!-- more -->
# 什么是 telegram

> Telegram 是跨平台的即时通信软件。其客户端是自由及开放源代码软件，但服务器是专有软件。用户可以相互交换加密与自毁消息，发送照片、影片等所有类型文件。（维基百科）

![Telegram](https://i.loli.net/2020/02/02/q65wpScnZjbEolv.jpg " ")

官方网站：[https://telegram.org/](https://telegram.org/)

如果打不开，应该是需要代理。插播一则小广告：

🛫️ 机场推荐：[卡车极速](http://m8.pw/mddv) | **优惠码：kc\_forever\_5**

# 创建机器人

首先需要添加 [@BotFather](https://telegram.me/BotFather)。

发送 `/start` 根据提示继续操作。

![创建机器人](https://i.loli.net/2020/02/02/CRr76xbFAz4v9pP.jpg " ")

输入 `/newbot` 创建机器人，然后是昵称，用户名（必须以 Bot 结尾）。

创建成功以后我们会得到一个 TOKEN，类似 `012345678:xxxxxxxxxxxxxxxxx-xxxxxxxxxx-xxxxxx`。

# 使用 Python 发送消息

## 安装

需要用到 [python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot) 库。

## First Blood

```python
import telegram

chat_id = "@xxxxx"  # 频道地址
token = ""  # 机器人 TOKEN
bot = telegram.Bot(token=token)
bot.send_message(chat_id=chat_id, text="测试")
```

是不是很简单？

## 发送带标题网址链接

```python
bot.send_message(
    chat_id=chat_id,
    text="<a href="https://lijianxun.top/">简讯的个人博客</a>",
    parse_mode=telegram.ParseMode.HTML
)
```

ParseMode 支持 html 和 markdown 两种格式。

html 标签支持加粗 `<b></b>`，斜体 `<i></i>` 等。

## 发送图片

```python
bot.send_photo(
    chat_id=chat_id,
    photo="https://i.loli.net/2019/09/19/PcQst46mwUaMEgV.png",
    caption="非必填的图片描述"
)
```

## 发送多张图片

完整代码

```python
import telegram
from telegram import InputMediaPhoto

photo_list = [
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png",
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png",
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png",
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png",
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png",
    "https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png"
]

result = [InputMediaPhoto(media=p, caption="知乎钓鱼贴")
          for p in photo_list]

bot.send_media_group(
    chat_id=chat_id,
    media=result
)
```

传相册的时候 `media` 需要 `InputMediaPhoto` 类型。

先记录这么多，以后用到其他类型再记录。