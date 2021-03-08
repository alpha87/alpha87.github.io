---
title: Docker 镜像下载慢怎么办
tags:
  - Docker
id: '94'
categories:
  - - 贵在折腾
date: 2019-07-07 17:52:52
---

国内使用 docker 下载镜像速度非常慢。
<!-- more -->
Registry mirrors 中输入：

```javascript
"registry-mirrors": [
    "http://f1361db2.m.daocloud.io",
    "https://dockerhub.azk8s.cn",
    "https://hub-mirror.c.163.com"
  ],
```

设置好重启。

![](https://i.loli.net/2020/02/09/zGDIE6Qfmys2WKA.png)