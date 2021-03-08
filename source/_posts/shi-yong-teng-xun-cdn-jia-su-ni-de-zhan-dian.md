---
title: 使用腾讯 CDN 加速你的站点
tags:
  - CDN
  - 腾讯云
id: '151'
categories:
  - - 贵在折腾
date: 2020-02-07 13:37:15
---

![](https://cdn.pixabay.com/photo/2018/12/10/10/21/earth-3866609__480.jpg)

## 什么是内容分发网络？

内容分发网络（Content Delivery Network，CDN），是在现有 Internet 中增加的一层新的网络架构，由遍布全国的高性能加速节点构成。这些高性能的服务节点都会按照一定的缓存策略存储您的业务内容，当您的用户向您的某一业务内容发起请求时，请求会被调度至最接近用户的服务节点，直接由服务节点快速响应，有效降低用户访问延迟，提升可用性。

CDN 有效地解决了目前互联网业务中网络层面的以下问题：

1.  用户与业务服务器地域间物理距离较远，需要进行多次网络转发，传输延时较高且不稳定。
2.  用户使用运营商与业务服务器所在运营商不同，请求需要运营商之间进行互联转发。
3.  业务服务器网络带宽、处理能力有限，当接收到海量用户请求时，会导致响应速度降低、可用性降低。

CDN 接入简单，您无需调整自身业务结构，或是进行复杂的操作配置，即可享受全球 CDN 加速服务。您可以通过 快速入门，轻松启动您的 CDN 加速服务。

以上部分来自[内容分发网络（腾讯云文档中心）](https://cloud.tencent.com/document/product/228/2939)。
<!-- more -->
话不多说，直接开始教程。

## 使用前提

1.  开通腾讯 CDN 服务（需要腾讯云账户实名认证）。
2.  域名备案。

初次使用腾讯 CDN 服务，会赠送 6 个月的每月 50G 流量，对于我这样的小站是绰绰有余了。（之后怎么计费我忘记了，好像是每月有 10G 流量？）

## 开始配置

### 接入域名

![域名管理界面](https://i.loli.net/2020/02/07/DzBYNet8gvZsUdp.png)

如果你是初次进入，肯定是没有配置的。点击左侧的域名管理，点击添加域名。

域名配置和源站配置：

![域名配置和源站配置](https://i.loli.net/2020/02/07/jLNcGfyDqk4egMI.png)

域名填入你需要加速的域名，这里我定义为 `cdn.lijianxun.top`，如果只是加速网站，选择静态加速即可。下载加速和流媒体点播加速分别对应下载服务和视频服务。

源站配置中源站地址填入域名对应 IP 地址，也就是网站没加速之前，域名绑定的主机 IP 地址。回源协议根据自身情况确定，可以选择协议跟随。回源域名和加速域名一致。

![加速服务](https://i.loli.net/2020/02/07/MZioxHyWtaSdCpY.png)

这里主要是静态文件缓存配置。基本上都是参考网上配置的，比较好理解。

缓存 `/wp-admin` 可能导致仪表盘打不开，所以这里刷新时间改为 0 秒，0 秒表示不缓存。

_不过完整一套配置下来感觉并没有用到 `/wp-admin` 这一条，下文解释。但是实在懒得尝试了，先这么用着。_

简单设置完成后，就可以配置详情了。

缓存过期配置：

![缓存过期配置](https://i.loli.net/2020/02/07/xSCt1lbqPEVfgeZ.png)

打开高级缓存过期设置。

高级配置中，我配置了以下几项：

![高级配置1](https://i.loli.net/2020/02/07/WuJiRoefkZGbCXs.png)

![高级配置0](https://i.loli.net/2020/02/07/TtHJzoCIil1jZGc.png)

HTTPS 证书可以使用腾讯云提供的免费证书，大概不到五分钟就申请成功。其他选项可以点开详情查看，都比较好理解，可以根据自己站点配置。

这样就算域名接入完成。

### 配置 Nginx

是这样，对于网站，我们加速 `wp-content` 和 `wp-includes` 两个文件夹，所以在访问源站（[https://lijianxun.top/）的时候，让](https://lijianxun.top/%EF%BC%89%E7%9A%84%E6%97%B6%E5%80%99%EF%BC%8C%E8%AE%A9) `wp-content` 和 `wp-includes` 这两个文件夹的内容走 CDN 加速。所以需要修改 Nginx 配置。

首先需要在源站 server 配置中加入以下内容：

```config
sub_filter 'https://resource.lijianxun.top/wp-content/' 'https://cdn.lijianxun.top/wp-content/';
sub_filter 'https://resource.lijianxun.top/wp-includes/' 'https://cdn.lijianxun.top/wp-includes/';
sub_filter_types *;
sub_filter_once off;
```

这样保证在访问 lijianxun.top 的时候，`wp-content` 和 `wp-includes` 会走加速域名。

配置 cdn.lijianxun.top

```config
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    
    ssl on;
    ssl_certificate /etc/nginx/cert/xxxx.pem;
    ssl_certificate_key /etc/nginx/cert/xxxx.key;
    
    server_name cdn.lijianxun.top;
    root /var/www/html;
    
    location /wp-includes {
        root /var/www/html;
        expires 30d;
    }
    
    location /wp-content {
        root /var/www/html;
        expires 30d;
    }
}
```

配置好之后记得重启 Nginx 服务：

`/etc/init.d/nginx restart`

这部分参考：[WordPress使用Nginx配合CDN加速静态资源](https://nodeedge.com/wordpress-nginx-cdn-static.html)

这部分解释了上文为什么用不到 `/wp-admin` 缓存配置。因为我们只加速了 `wp-content` 和 `wp-includes`。

### 配置 CNAME

在接入域名后，我们会得到一个 CNAME，格式类似：`加速域名.cdn.dnsv1.com`，如果按照教程来就是 `cdn.lijianxun.top.cdn.dnsv1.com`。

因为我的域名是在阿里云下购买的，不过配置域名解析大同小异。如图：

![配置 CNAME](https://i.loli.net/2020/02/07/3qemSYPgvyAhH4K.png)

这样就配置好了。

## 测试

当我们全部都配置完成后，使用 Chrome 浏览器，打开开发者工具，会发现配置的 `wp-content` 和 `wp-includes` 全部都走的是加速域名，网站访问也一切正常。

## 后记

之前从来没折腾过 CDN 加速，以上内容都是翻前人记录，自己摸索的，如果有读者发现明显错误还请劳烦在下方留言，共同学习，感谢！🙏