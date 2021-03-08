---
title: 基于Hexo的个人博客搭建
tags:
  - hexo
  - 博客
id: '32'
categories:
  - - 贵在折腾
date: 2017-10-15 09:59:26
---

![](https://i.udemycdn.com/course/750x422/1406428_0f0a.jpg) 之前介绍了基于 typecho 框架搭建个人网站的教程，有部分小伙伴问有没有不用服务器的免费博客框架，还真有。那就是有名的 Hexo。这个博客框架属于静态博客的一种，只要将 Hexo 文件部署到 GitHub 页面上，博客就可以运行了，同时也会生成域名，连购买域名的钱都省下来了。
<!-- more -->
我们的博客是基于 Hexo 的，官网的描述只有一句话：

> A fast, simple, powerful blog framework.

当你看完这篇教程以后，你会觉得的确是这样。

# 安装依赖

在使用 Hexo 之前，我们必须安装 Node.js 运行环境。 简单的说 Node.js 就是运行在服务端的 JavaScript，一个基于 Chrome JavaScript 运行时建立的一个平台，一个事件驱动 I/O 服务端 JavaScript 环境，基于 Google 的 V8 引擎，V8 引擎执行 Javascript 的速度非常快，性能非常好。

## 安装 Node.js

首先要去官网安装 Node.js。 官网地址：[https://nodejs.org/en/](https://nodejs.org/en/) ![安装 Nodejs](https://i.loli.net/2020/04/16/XGPDVT1MhmtjQuB.png) 网站会自动判断操作系统，这里选择 12.16.2 LTS，下载后直接安装即可。

## 设置淘宝npm镜像

安装好 Node.js 之后，首先要做的事是使用淘宝 npm 镜像加速，不然下载速度巨慢。 输入命令：

```bash
npm config set registry https://registry.npm.taobao.org
```

# 安装 Hexo

```bash
npm install hexo-cli -g
```

如果没有报错，那么就可以开始搭建属于自己的博客了。如果报错，应该是权限问题。 Windows 系统使用管理员模式运行 cmd，如果是 Linux 或 Mac 可以在命令前添加 sudo 使用管理员模式执行命令。

# 运行

只需要简单的 4 行命令，即可运行博客：

```bash
hexo init blog

cd blog

npm install

hexo s -g
```

具体介绍一下这四行命令。

1.  `hexo init blog` 在当前路径下创建 blog 文件夹，用来初始化博客框架，存放配置文件和代码。接下来需要等待 Hexo 框架在 blog 文件夹下自动生成一些文件。
2.  `cd blog` 进入 blog 文件夹下。
3.  `npm install` 安装博客所需的依赖。
4.  `hexo s -g` 生成静态页面和启动本地服务的命令：启动成功会提示打开 `http://localhost:4000/`，按下 `Ctrl+C` 停止。

打开网址就可以看到刚才生成的静态页面了。 ![博客首页](https://i.loli.net/2020/04/16/e1ONHa4gobfUCGy.png) 这里有一些 Hexo 常用的命令：

```bash
hexo new"postName"           # 新建文章
hexo new page"pageName"      # 新建页面
hexo generate                # 生成静态页面至 public 目录
hexo server                  # 开启预览访问端口（默认端口 4000，ctrl + c 关闭 server）
hexo deploy                  # 将 .deploy 目录部署到指定空间
hexo help                    # 查看帮助
hexo version                 # 查看 Hexo 的版本
```

# 部署环境

我们把博客部署到 GitHub 上，所以首先要有 GitHub 账户，如果你已经申请过就可以跳过这一步了。

## 搭建仓库

进入 Github 网站注册一个账号，完成邮箱认证就可以开始项目了。 在 Repository name 一栏填入 `你的用户名+github.io`，比如我的用户名是 alpha87, Repository name 就写 alpha87.github.io。注意仓库名称格式，一定是 yourname.github.io。

## 部署公钥

进入账号的 Personal setting 页面，选择 SSH and GPG keys，进入密钥配置页面。填入本机公钥，点击 Add SSH key。 到这里，你一定会问，SSH 密钥对是什么，在哪能找到。接下来就告诉你如何获取密钥。

## 获取密钥

### Windows 系统

首先需要下载 git for windows。 下载好以后在桌面单击右键运行 `Git Bash here`，输入： `ssh-keygen -t rsa -C "git 邮箱"`

### Mac or Linux

如果是 Mac 或者 Linux 系统，直接输入上边那条命令，因为 Mac 或 Linux 系统自带 git。 `ssh-keygen -t rsa -C "git 邮箱"` 然后会提示你输入 key 的名称，直接回车，之后弹出来什么都按回车默认值就行。 然后在用户目录下找到 .ssh 目录，找到 id\_rsa.pub 文件，用记事本打开，复制到刚才需要添加的 SSH key 里就可以了。 然后输入： ssh -T [git@github.com](mailto:git@github.com) 当你第一次使用 Git 的 clone 或者 push 命令连接 GitHub 时，会得到一个警告：

```bash
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```

输入 `yes`，如果返回 `Hi yourname! You’ve successfully authenticated, but GitHub does not provide shell access.` 则说明公钥部署成功。

# 发布站点

想要发布站点（也就是使用域名访问）还得设置一下，打开 blog 目录下的 \_config.yml。在最下边找到 deploy：

```bash
deploy:
  type: git
  repository: git@github.com:yourname/yourname.github.io.git
  branch: master
```

yourname 是你的用户名。 先输入命令：

```bash
npm install hexo-deployer-git --save
```

然后输入：

```bash
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

然后部署： `hexo d -g` 这样操作以后，浏览器打开 [https://yourname.github.io](https://yourname.github.io) 就可以看到刚才本地预览页面了。

# 丰富的主题和插件

博客不能少了丰富的主题，主题越多个性化也就越强。Hexo 有很多主题供你选择。 ![](https://i.loli.net/2020/04/16/WkMa3pshgqVjXoH.png) Hexo 也提供了大量的插件，帮助用户更好的使用。例如字数统计，用户访问量等等都有插件来完成。 ![](https://i.loli.net/2020/04/16/oTfgJkqmWbISDZx.png) 快去尝试一下吧。