---
title: Ubuntu16.04 基于 LAMP 搭建 Wordpress
tags:
  - wordpress
  - 云服务器
  - 博客
id: '117'
categories:
  - - 贵在折腾
date: 2019-01-11 12:19:17
---

![](https://cdn.pixabay.com/photo/2014/12/28/13/20/wordpress-581849__480.jpg) 如果尝鲜Ubuntu18.04有些操作会不同，需要自行google。 默认root用户下操作。
<!-- more -->
## 前言

准备搭建博客的话，首先需要配置服务器的**安全组规则**，开放80端口。 如果搭建好wordpress，访问却怎么都不成功，可能就是由于端口限制。 先执行 `apt-get update && apt-get upgrade -y` 来更新软件包。

## 搭建LAMP环境

> LAMP也就是Linux，Apache，Mysql，PHP的缩写。

执行 `apt-get install -y apache2 mysql-server php7.0 libapache2-mod-php7.0 php7.0-mysql phpmyadmin` 来安装这些包。 安装期间mysql和phpmyadmin会提示输入密码。 如果你的操作系统是ubuntu 18.04，可能在安装libapache2-mod-php7.0时会有些问题。 可以这样尝试一下： `add-apt-repository ppa:ondrej/php` `apt-get update` `apt-get install -y libapache2-mod-php7.0 php7.0-mysql` 这样应该就能安装成功了。全部安装完成之后，就可以继续操作了。 **修改mysql配置文件**： `vim /etc/mysql/mysql.conf.d/mysqld.cnf` 使用`#`注释掉`bind-address = 127.0.0.1`。 进入数据库。 `mysql -u root -p` 会提示输入密码，直接输入即可，回车完成输入。 **创建wordpress数据库，并提供访问权限**：

```mysql
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON *.* to root@'%' identified by 'root' with grant option;
FLUSH PRIVILEGES;
exit;
```

**重启mysql和apache。** `service apache2 restart` `service mysql restart`

## 安装wordpress

为了之后不必要的麻烦，还是推荐安装简体中文包。 官网地址：[https://cn.wordpress.org/download/](https://cn.wordpress.org/download/) 复制好tar.gz后缀的下载链接，此时的链接为5.0.3版本。 `https://cn.wordpress.org/wordpress-5.0.3-zh_CN.tar.gz` 移步到机器上输入： `wget https://cn.wordpress.org/wordpress-5.0.3-zh_CN.tar.gz` 下载完成后解压 `tar zxvf wordpress-5.0.3-zh_CN.tar.gz -C /var/www/html/` 如果没有什么特殊情况，wordpress应该已经安装完成。 解压完成后执行 `chmod -R 777 /var/www/html/` 这条命令保证在之后的wordpress配置和操作不受权限影响。 **一定要记得将`/var/www/html/`目录下的index.html重命名。否则之后在进入主页的时候永远显示的是apache的页面。** 可以使用这条命令重命名： `mv /var/www/html/index.html /var/www/html/index-apache.html` 好了，接下来配置wordpress。

## 配置wordpress

在浏览器地址栏输入你的`公网ip/wordpress`，应该就会提示你配置一些信息。 填写数据库信息就是你机器上的数据库用户名和密码。 根据提示一步一步操作。 然后，大功告成！ **开始你的博客生活吧！**