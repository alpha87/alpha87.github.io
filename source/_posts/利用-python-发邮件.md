---
title: 利用 Python 发邮件
tags:
  - Python
  - 脚本
id: '39'
categories:
  - - 编程学习
date: 2016-12-09 10:18:08
---

![](https://cdn.pixabay.com/photo/2018/03/22/02/37/email-3249062__480.png) 在搜索python教程的时候，碰巧遇到了「 利用 python 发送电子邮件 」的教程，觉得很实用也比较有意思，所以就花了点时间学习发送邮件的脚本是怎样实现的。 我主要是利用SMTP（简单邮件传输协议）来实现邮件的发送。
<!-- more -->
首先要安装SMTP和email的库 运行cmd，输入：

```
pip install secure-smtplib email
```

这样我们所需的库就安装好了。 网上大部分的教程，说什么好呢，有些时候我真的很奇怪，他们在写完教程以后会不会再按着自己的教程运行一遍，因为我一步一步按着教程做下来，还是会提示我各种错误，猝不及防。不过这个教程遇到的错误，最终还是解决了，因为解决办法是在手机上搜到的，那份教程找不到，网站就贴不上来了。进入正题，贴代码：

```python
#coding: utf-8

import smtplib
from email.mime.text import MIMEText
from email.header import Header


sender = raw_input("Please enter a sender email address:")  # 发件人邮箱地址
password = raw_input("keywords:")  # 密码
receiver = raw_input("Please enter a receiver address:")  # 收件人邮箱地址
subject = raw_input("Title:")  # 邮件标题
words = raw_input("Text:")  # 邮件正文
smtpserver = 'smtp.163.com'
msg = MIMEText(words, 'plain', 'utf-8')  # 中文需参数 utf-8，单字节字符不需要
msg['Subject'] = Header(subject, 'utf-8')  # 邮件标题
msg['from'] = sender  # 发信人地址
msg['to'] = receiver  # 收信人地址
smtp = smtplib.SMTP()
smtp.connect('smtp.163.com')
smtp.login(sender, password)
smtp.sendmail(sender, receiver, msg.as_string())  # 这行代码解决的下方554的错误
smtp.quit()
print "邮件发送成功!"
```

这个脚本比较简单，但是可以发送成功，网上有些教程运行会提示错误：

> `554 DT:SPM` 发送的邮件内容包含了未被许可的信息，或被系统识别为垃圾邮件。请检查是否有用户发送病毒或者垃圾邮件；

在上边代码标注了，我找到的解决办法主要和那行代码有关系，为什么我还没有弄明白。这段代码还有一个缺点是邮件的标题只能为英文，如果换成中文的话会提示编码错误： `UnicodeDecodeError: ‘utf8’ codec can’t decode byte 0xb1 in position 0: invalid start byte` **需要注意的是，如果你用的是163邮箱，需要设置客户端授权密码。** ![](http://upload-images.jianshu.io/upload_images/3880264-ecfd60f843c96414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 在“设置”里找到“客户端授权密码”，开启并输入你要设置的密码，在python代码中用到的密码就是你设置的“客户端授权密码”。 测试效果： ![](http://upload-images.jianshu.io/upload_images/3880264-97c5164eeb752013.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)