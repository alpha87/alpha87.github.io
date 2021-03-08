---
title: 解决 Electron 安装慢问题
tags:
  - Electron
  - 终端
id: '322'
categories:
  - - 贵在折腾
date: 2020-03-14 16:50:16
---

即使使用了淘宝源同样很慢很慢，卡到超时。
<!-- more -->
```bash
npm install electron -g --registry http://registry.cnpmjs.org
/Users/lijianxun/.npm-global/bin/electron -> /Users/lijianxun/.npm-global/lib/node_modules/electron/cli.js

> electron@8.1.1 postinstall /Users/lijianxun/.npm-global/lib/node_modules/electron
> node install.js

Downloading electron-v8.1.1-darwin-x64.zip: [----------------------------------------------------------------------------------------------------] 0% ETA: 0.0 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=------------------------------------------------------------------------------------------------] 1% ETA: 1001.0 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=------------------------------------------------------------------------------------------------] 1% ETA: 5341.0 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 16632.8 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 29791.6 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 30169.9 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 46042.1 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 55368.6 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 66519.9 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 76038.8 seconds 
Downloading electron-v8.1.1-darwin-x64.zip: [=-----------------------------------------------------------------------------------------------] 1% ETA: 93503.3 seconds
```

看到这样的进度条就头疼，如何加速下载安装 Electron 呢？

# 解决办法：

`npm config set electron_mirror http://npm.taobao.org/mirrors/electron/`

`npm config set electron_custom_dir "8.1.1"`

然后再全局安装 Electron：

`npm install electron -g`

# 效果

安装只需 12.91 秒。

![](https://cdn.jsdelivr.net/gh/bzwys/blog-imgs@master/imgs/20200314165719.png)

> 参考：[解决electron被墙问题](https://www.hellojava.com/a/86921.html)