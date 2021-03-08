---
title: 使用树莓派配合 AdguardHome 屏蔽局域网内广告
tags:
  - Adguard
  - 屏蔽广告
  - 树莓派
id: '50'
categories:
  - - 贵在折腾
date: 2019-02-01 10:36:39
---

![](https://cdn.pixabay.com/photo/2018/09/14/06/50/raspberry-pi-3676379__480.jpg) AdGuard算是比较强大的广告拦截工具了。爱奇艺，腾讯视频等主流视频网站的广告都可以被拦截，而且是打开视频后直接跳过广告，不像其他插件还需要等待。屏蔽视频广告只是其功能的一部分，还能保护隐私，屏蔽钓鱼网站等等。
<!-- more -->
使用树莓派屏蔽广告使用的是AdGuard的开源项目AdGuard Home。如果家里有正在吃灰的树莓派，可以拿来试试。

## 安装

打开终端，输入： `wget https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.92-hotfix1/AdGuardHome_v0.92-hotfix1_linux_arm.tar.gz` 下载完成后解压： `tar zxvf AdGuardHome_v0.92-hotfix1_linux_arm.tar.gz` 运行： `./AdGuardHome -s install && AdGuardHome -s start` 来启动服务。 如果没有报错继续输入 `host doubleclick.net 127.0.0.1` 来测试服务是否生效。正常情况会返回：

```bash
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

Host doubleclick.net not found: 3(NXDOMAIN)
```

这样就完成了安装。

## 配置

打开`http://192.168.x.xxx:3000/`，记得更换为树莓派的ip地址，就能对AdGuard Home配置了。

## 使用

官方wiki中也有介绍，以下内容基本就是翻译过来的版本。

### 路由器

此设置将自动覆盖连接到家庭路由器的所有设备，您无需手动配置每个设备。 打开路由器的首选项。通常，您可以通过URL（例如`http://192.168.0.1/`或`http://192.168.1.1/`）从浏览器访问它。系统可能会要求您输入密码。如果您不记得它，您通常可以通过按路由器本身上的按钮来重置密码。某些路由器需要特定的应用程序，在这种情况下应该已经安装在您的计算机/电话上。 找到DHCP / DNS设置。查找字段旁边的“DNS”字母，该字母允许两组或三组数字，每组数字分为四组，每组三到三位。 在那里输入您的AdGuard Home服务器地址。

### Android

在Android菜单主屏幕中，点按“设置”，点按菜单上的Wi-Fi。这将显示所有可用网络的列表（无法为移动连接设置自定义DNS）。长按您连接的网络，然后点按“修改网络”。 在某些设备上，您可能需要选中“高级”复选框以查看更多设置。要调整Android DNS设置，您需要将IP设置从“DHCP”切换为“静态”。 将“DNS 1”和“DNS 2”值更改为您的AdGuard Home地址。

### iOS

在主屏幕中，点按“设置”，在左侧菜单中选择“Wi-Fi”（无法为移动网络配置DNS）。点击当前活动网络的名称。 在“DNS”字段中输入您的AdGuard Home地址。

### 苹果系统

单击Apple图标，然后转到“系统偏好设置”。 点击“网络”。 选择列表中的第一个连接，然后单击“高级”。 选择“DNS”标签，然后添加AdGuard Home地址。

### Windows

通过“开始”菜单或Windows搜索打开控制面板，转到“网络和Internet”类别，然后转到“网络和共享中心”。 在屏幕左侧找到“更改适配器设置”并单击它，选择您的活动连接，右键单击它并选择“属性”。 在列表中找到“Internet协议版本4（TCP / IP）”，选择它，然后再次单击“属性”。 选择“使用以下DNS服务器地址”并在那里输入您的AdGuard Home地址。 这样就大功告成了。 ![来自官网的截图](https://cdn.adguard.com/public/Adguard/Blog/AGHome/dashboard.jpg)