---
title: 使用 Sakura Frp 实现内网穿透
tags:
  - 内网穿透
id: '290'
categories:
  - - 好物推荐
date: 2020-02-28 18:00:01
---

内网穿透，简单说就是通过外网访问内网。再说的通俗一点就是实现手机使用蜂窝数据来连接家中的服务器（树莓派）。
<!-- more -->
# 为什么使用内网穿透

比如说家中有一台闲置的电脑或者树莓派，可以在其上面搭建博客。一般情况搭建好只能通过内网 IP 访问。这时就需要用到内网穿透服务，让外网也可以访问我们搭建好的网站。

# Sakura Frp

[SAKURA FRP](https://www.natfrp.com/) 提供免费的内网穿透服务，并且拥有强大的服务器资源。

![](https://i.loli.net/2020/02/28/xA3phTQNJXzy7Si.png)

我们注册好账号就可以开始使用。使用内网穿透服务就需要创建一个内网穿透隧道。可以看到还是有很多隧道供我们选择的。

![隧道](https://i.loli.net/2020/02/28/XMjBSqCgVp7AfE8.png)

# 配置

这里我使用树莓派 Adguard Home 的管理页面作为示例，博客或者网站和这个配置一样。

在本地，我通过 **192.168.31.181:80** 访问到这个页面：

![adguard](https://i.loli.net/2020/02/28/QvcDwlW5YxuzLh1.png)

如何通过配置 Sakura Frp 来访问远程访问这个页面呢？

如果你的域名没有备案，在选择服务器一栏需要选择非大陆服务器。

隧道类型根据自己的实际情况选择 HTTP 或 HTTPS。

本地地址填写默认的 127.0.0.1 即可。

本地端口填写本地网站映射的端口，比如说 Adguard Home 的管理页面是 80 端口，这里就填写 80 端口，如果你的网站开启了 SSL，就是 443 端口。

远程端口随便填写。映射的端口最小为 10240，最大为 65535。

绑定域名就是你网站的域名。这里使用 adguard.lijianxun.top。

![](https://i.loli.net/2020/02/28/eJpkj5NA7lGryIR.png)

# 下载 Frp 客户端

通过 ssh 连接到树莓派，下载客户端。

```bash
wget https://qianqu.me/frp/frpc_linux_arm
```

下载好以后设置权限：

```bash
sudo chmod +x frpc_linux_arm
```

运行：

```bash
./frpc_linux_arm
```

密钥在用户信息中可以查看，服务器 ID 在终端查看。例如我们选择香港云地，对应 ID 是 27。

![终端](https://i.loli.net/2020/02/28/IX3E28qfnidUpoO.png)

# 域名解析

![域名解析](https://i.loli.net/2020/02/28/qIiZ9PUa3nou27H.png)

设置好域名解析之后，我们就能通过自定义域名访问网页了。

![adguard](https://i.loli.net/2020/02/28/ByFfX4s2xmlcCSR.png)