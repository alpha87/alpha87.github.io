---
title: 基于 LNMP 搭建 Wordpress 博客
tags:
  - wordpress
  - 博客
id: '34'
categories:
  - - 贵在折腾
date: 2019-09-17 10:02:34
---

![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/WordPress.svg/698px-WordPress.svg.png) 原谅我最终还是把博客从 hexo 移到了 wordpress。之前搭建 wordpress 使用 Apache，但是已经习惯了 nginx，所以记录一波这次搭建网站的过程。

# 安装 LNMP

服务器版本是 Ubuntu 16.04.6 LTS。 首先我们需要运行命令 `apt-get update` 更新软件源列表。 然后一口气安装完 nginx，mysql，PHP。 `apt-get install -y nginx mysql-server php-fpm php-mysql` 安装过程中会提示输入数据库的密码，之后等待安装完成。 查看 nginx 版本:

```bash
➜  ~ nginx -v
nginx version: nginx/1.10.3 (Ubuntu)
```

# 配置 nginx 并实现 https 访问

先在域名提供商购买免费证书，然后下载 nginx 服务器类型的证书。 ![屏幕快照 2019-09-18 上午8.37.13.png](https://i.loli.net/2019/09/18/V9o5Sh32ZPuMFG4.png) 下载好的是一个压缩包，解压并新建文件夹，用来存放 `pem` 和 `key` 为后缀的证书文件。 `mkdir /etc/nginx/cert` 更改 nginx 配置，编辑文件： `vim /etc/nginx/sites-available/default` 使用 vim 命令 `ggdG` 删除所有文本，复制以下内容： **最好提前购买域名并备案，server\_name 记得改成自己的域名！** 这个配置自动将 http 重定向到 https。

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    rewrite ^(.*)$ https://$host$1 permanent;
}

server {
    listen 443 ssl default_server;
    listen [::]:443 ssl default_server;

    ssl on;
    ssl_certificate /etc/nginx/cert/yourcertfile.pem;
    ssl_certificate_key /etc/nginx/cert/yourcertfile.key;

    root /var/www/html;

    index index.php index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

使用 `nginx -t` 测试配置文件是否正确。

# 配置数据库

## 修改mysql配置文件

`vim /etc/mysql/mysql.conf.d/mysqld.cnf` 使用 `#` 注释掉 `bind-address = 127.0.0.1`。 进入数据库。 `mysql -u root -p` 会提示输入密码，直接输入即可，回车完成输入。

## 创建 wordpress 数据库，并提供访问权限

```mysql
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON *.* to root@'%' identified by 'root' with grant option;
FLUSH PRIVILEGES;
exit;
```

## 重启 nginx 和 mysql 服务

```bash
/etc/init.d/nginx restart
/etc/init.d/mysql restart
```

# 下载 wordpress

为了之后不必要的麻烦，还是推荐安装简体中文包。 官网地址：[https://cn.wordpress.org/download/](https://cn.wordpress.org/download/) 复制好 `tar.gz` 后缀的下载链接，此时的链接为 5.0.3 版本。 `https://cn.wordpress.org/wordpress-5.0.3-zh_CN.tar.gz` 移步到机器上输入： `wget https://cn.wordpress.org/wordpress-5.0.3-zh_CN.tar.gz` 下载完成后解压 `tar zxvf wordpress-5.0.3-zh_CN.tar.gz -C /var/www/html/` 如果没有什么特殊情况，wordpress 应该已经安装完成。 解压完成后先删除 html 路径下的 index.html 文件，然后进入 wordpress 目录： **删除 index.html** `rm /var/www/html/index.html` **进入 Wordpress 目录** `cd /var/www/html/wordpress` **复制所有文件到上一路径** `cp -r * ..` 然后授权该路径，避免在之后 wordpress 配置或是安装插件等有不必要麻烦： `chmod -R 777 /var/www/html/` 这条命令保证在之后的wordpress配置和操作不受权限影响。

# 大功告成！

好了，接下来配置wordpress。 在浏览器输入你的公网 ip，如果已经绑定域名直接输入域名即可，然后一步一步配置自己的博客吧！