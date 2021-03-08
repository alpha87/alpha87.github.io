---
title: 终端 Proxychains4 使用记录
tags:
  - 科学上网
  - 终端
id: '83'
categories:
  - - 贵在折腾
date: 2018-10-15 12:41:28
---

![](https://cdn.pixabay.com/photo/2017/10/24/07/12/hacker-2883632__480.jpg) 因为日常生活中经常遇到使用wget下载文件且文件在国内下载比较慢或不能下载的情况, 需要在终端使用代理, 试过一些方法, 还是觉得使用proxychains更方便一些.
<!-- more -->
## 下载安装

Mac下: `brew install proxychains-ng` 其他: [proxychains-ng](https://github.com/rofl0r/proxychains-ng)提供了编译安装:

```
*** Installation ***

  # needs a working C compiler, preferably gcc
  ./configure --prefix=/usr --sysconfdir=/etc
  make
  [optional] sudo make install
  [optional] sudo make install-config (installs proxychains.conf)

  if you dont install, you can use proxychains from the build directory
  like this: ./proxychains4 -f src/proxychains.conf telnet google.com 80
```

## 配置

编辑`/usr/local/etc/proxychains.conf`, 在最后一行改为: `socks5 127.0.0.1 1080` **1080**是本地socks5监听端口, 需要修改成你自己配置的.

## 使用

测试一波: `proxychains4 wget https://www.google.com` 不加代理明显会超时, 加代理后会提示:

```
正在解析主机 www.google.com (www.google.com)... 224.0.0.1
正在连接 www.google.com (www.google.com)|224.0.0.1|:443... [proxychains] Strict chain  ...  127.0.0.1:1080  ...  www.google.com:443  ...  OK
已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
```