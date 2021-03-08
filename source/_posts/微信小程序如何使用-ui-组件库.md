---
title: 微信小程序如何使用 UI 组件库
tags:
  - 微信小程序
id: '69'
categories:
  - - 前端
date: 2019-05-28 11:22:33
---

![](https://cdn.pixabay.com/photo/2020/02/01/01/06/urban-4809320__480.jpg) 在创建小程序的时候，有些样式并不完全需要自己来写。 可以使用已经构建好的框架，比如官方的 UI 组件库 [WeUI](https://weui.io/)。 我们本次使用的组件库是有赞的 [Vant Weapp](https://youzan.github.io/vant-weapp/#/intro)。 使用基于小程序云开发，和本地开发大同小异。
<!-- more -->
# npm init

使用云开发会在项目下自动生成一个 `miniprogram` 文件夹，单击右键，选择**终端打开**。 在终端中输入`npm init`，初始化项目。

# 安装 Vant Weapp 库

初始化完成后，在终端输入： `npm i vant-weapp -S --production` 来安装 Vant Weapp 组件库。

# 构建 npm

在微信开发者工具的菜单栏找到**工具**，点击**构建 npm**，构建完成后在右侧**详情**中开启**使用 npm 模块**选项。 ![构建 npm](https://user-images.githubusercontent.com/25655581/59421890-fc44f580-8e01-11e9-9df1-90d224a7dbe2.png) ![使用 npm 模块](https://user-images.githubusercontent.com/25655581/59421893-fea74f80-8e01-11e9-8b34-40f795cd6a5c.png)

# 引用组件库

在需要使用 Vant 组件库的页面的 json 文件中编辑：

```json
{
  "usingComponents": {
    "van-button": "vant-weapp/button"
  }
}
```

这样就完成了组件的引用。