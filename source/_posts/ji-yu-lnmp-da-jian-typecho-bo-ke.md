---
title: 基于 LNMP 搭建 Typecho 博客
tags:
  - typecho
  - 博客
id: '314'
categories:
  - - 贵在折腾
date: 2020-03-11 14:29:39
---

![typecho](https://i.loli.net/2020/03/11/L3GAvD64g85qtZ1.png)

Typecho 是除了 Wordpress 又一博客系统，非常轻量。
<!-- more -->
Typecho 都有哪些特点呢？

**1\. 轻量高效：**仅仅 7 张数据表，加上不足 400KB 的代码，就实现了完整的插件与模板机制。超低的 CPU 和内存使用率，足以发挥主机的最高性能。

**2\. 先进稳定：**原生支持 Markdown 排版语法，易读更易写。支持 BAE/GAE/SAE 等各类云主机，即使面对突如其来的高访问量，也能轻松应对。

**3\. 简洁友好：**精心打磨过的操作界面，依然是你熟悉的面孔，更多了一份成熟与贴心。每一个像素的剪裁，都只为离完美更进一步。

所以，如果你不喜欢 Wordpress 保姆式的博客系统，只想简单写写文字，自定义主题，可以试试 Typecho。

# LNMP

服务器版本是 Ubuntu 16.04.6 LTS。

```bash
sudo apt-get install -y nginx mysql-server php-fpm php-xml php-mysql
```

安装过程中会提示输入数据库的密码，之后等待安装完成。

看过[《基于 LNMP 搭建 Wordpress 博客》](https://lijianxun.top/34.html)这篇文章的朋友可能觉得安装 LNMP 的命令一样，其实在本文安装过程中多了一个 `php-xml`。

如果没有 `php-xml`，可能在安装完成以后会报错，**提示 `Call to undefined function utf8_decode()`**。

## 配置 nginx

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;

    index index.php index.html index.htm;

    server_name _;

    location / {
        if (-f $request_filename/index.html){
            rewrite (.*) $1/index.html break;
        }
        if (-f $request_filename/index.php){
            rewrite (.*) $1/index.php;
        }
        if (!-f $request_filename){
            rewrite (.*) /index.php;
        }
        try_files $uri $uri/ =404;
    }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/run/php/php7.0-fpm.sock;
  }
}
```

下边这部分配置是为了防止后期安装完成后，**文章详情页和后台页面出现 404 错误**。

```bash
if (-f $request_filename/index.html){
    rewrite (.*) $1/index.html break;
}
if (-f $request_filename/index.php){
    rewrite (.*) $1/index.php;
}
if (!-f $request_filename){
    rewrite (.*) /index.php;
}
```

## 配置数据库（可先跳过）

`mysql -u root -p`

输入密码，进入数据库。

`create database typecho;`

创建 typecho 数据库，在之后安装的时候可以避免**对不起，无法连接数据库，请先检查数据库配置再继续进行安装**这个错误，不过这一步可以先忽略，如果之后安装出现问题，可以回来查看。

# Typecho

Typecho 官网的下载链接：[http://typecho.org/download。](http://typecho.org/download%E3%80%82)

目前最新版是 Typecho 1.1(17.10.30)，下载链接为：

[http://typecho.org/downloads/1.1-17.10.30-release.tar.gz](http://typecho.org/downloads/1.1-17.10.30-release.tar.gz)

## 下载

`wget http://typecho.org/downloads/1.1-17.10.30-release.tar.gz`

## 解压

`tar zxvf 1.1-17.10.30-release.tar.gz`

## 移动到 /var/www/html/

`sudo mv build/* /var/www/html/`

## 赋予权限

`cd /var/www/html/ && sudo chmod 777 *`

## 安装

在浏览器中输入服务器的公网 IP，后接 install.php。就进入了安装页面。根据提示完成安装。

# 大功告成

![安装完成](https://i.loli.net/2020/03/11/2TLJyQd5wqeZ63z.png)

如果按照上文配置 nginx 和 mysql，当安装完成后，再次访问公网 IP，会显示此界面，说明安装成功。