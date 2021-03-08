---
title: Wordpress 博客系统搭建好必做的几件事
tags:
  - wordpress
id: '177'
categories:
  - - 贵在折腾
date: 2019-10-10 12:33:22
---

![](https://cdn.pixabay.com/photo/2014/02/13/07/28/wordpress-265132__480.jpg)

之前我们已经在 Ubuntu 16.04 系统上，分别[基于 LAMP](https://lijianxun.top/117.html) 和[基于 LNMP](https://lijianxun.top/34.html) 搭建了 Wordpress 博客系统。但是搭建好以后仍有许多工作要做，要不然用起来还是会别扭。
<!-- more -->
## FTP 账号问题

这个问题一定所有人都经历过。刚搭建好博客，提示 wordpress 版本，或者插件，或者主题更新，然后让我们输入 FTP 账号和密码。但是从头到尾配置的过程中我们并未涉及到 FTP 相关的配置，那怎么办呢？

进入到我们安装 wordpress 的目录里，找到 wp-config.php 文件，在其中加入如下三行：

```bash
define("FS_METHOD", "direct");
define("FS_CHMOD_DIR", 0777);
define("FS_CHMOD_FILE", 0777);
```

即可解决此问题。

## 上传主题或插件提示文件过大

这个问题有两种报错方式，一种是 Nginx 报 **413 Request Entity Too Large** 错误，另一个就是在 wordpress 内部提示**上传的文件尺寸超过 PHP.ini 中定义的 upload\_max\_filesize 值**。

所以我们需要改两个地方。

### 修改 Nginx

我们需要修改 nginx.conf 文件，一般在 /etc/nginx 下。

`vim /etc/nginx/nginx.conf`

找到（或添加） `client_max_body_size` 字段，修改为 `20m`

### 修改 php.ini

我们使用 `find / -name php.ini` 找到 `php.ini` 文件，使用 vim 打开，搜索 `upload_max_filesize` 和 `post_max_size`，均改为 `20m`。

_在 vim 中可以使用 `/关键词` 的方式来搜索。_

## wordpress 伪静态

在 wordpress 仪表盘里找到 设置->固定连接。

默认为朴素：`https://lijianxun.top/?p=123`，但是这种链接不利于搜索引擎的收录，也**不能正确生成 sitemap.xml 文件**。

所以我们需要改为 `https://lijianxun.top/123.html`，也就是所谓的伪静态。

我们在自定义结构中填入 `/%post_id%.html`，保存。

然后修改 nginx 中的 `server` 配置。

```bash
location / {
    index index.php index.html index.htm;
    try_files $uri $uri/ /index.php?$args;
}
```

改为以上内容即可。