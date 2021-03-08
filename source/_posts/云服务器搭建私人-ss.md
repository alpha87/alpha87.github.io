---
title: 云服务器搭建私人 SS
tags:
  - 云服务器
  - 科学上网
id: '82'
categories:
  - - 贵在折腾
date: 2017-04-15 11:38:50
---

![](https://cdn.pixabay.com/photo/2016/05/13/12/29/urban-1389838__480.jpg) 如果你有一台海外的云服务器, 最简单的操作就是搭梯子了. 总得看看墙外的世界.
<!-- more -->
## 准备工作

首先必须有一台香港或者海外的云服务器, 能搭梯子, 而且搭建blog还不用备案.

## 安装

执行: `apt-get install -y python-pip` 和 `pip install shadowsocks`

## 配置

编辑配置文件: `vim /etc/shadowsocks.json` 输入:

```json
{
  "server": "0.0.0.0",
  "server_port": 8388,
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "password": "Your Password",
  "timeout": 300,
  "method": "aes-256-cfb",
  "fast_open": true
}
```

保存即可.

## 开放端口

登录到云服务器的管理中心, 编辑相应服务器的安全组规则，新增一行。 规则协议选择 TCP，端口选择8388，保存即可。

## 运行

`ssserver -c /etc/shadowsocks.json -d start` 如果没有报错, 那么赶快试一下吧!