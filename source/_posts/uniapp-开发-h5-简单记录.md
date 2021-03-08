---
title: Uniapp 开发 H5 简单记录
tags:
  - uniapp
  - 宅宅生活收藏夹
id: '118'
categories:
  - - 前端
date: 2020-02-02 22:20:40
---

![](https://i.loli.net/2020/02/06/VigbIOD84WrmTAp.png) 之前那台服务器过期了，所以网页版的宅宅生活收藏夹暂时不能用。 本来想就此放弃，但是有很多朋友私信问我，所以考虑还是继续开通这个知乎看图平台吧。
<!-- more -->
**毕竟这里收录了全网知乎钓鱼贴最全的问题。**

# 前言

但是之前那个前端页面，说实话我自己也接受不了。因为当时还没有看过 Vue 相关的教程，只能用 Jinja2 配合 Bulma 构建前端页面。

> [Jinja2](http://docs.jinkan.org/docs/jinja2/) 是一个现代的，设计者友好的，仿照 Django 模板的 Python 模板语言。 [Bulma](https://bulma.io/) 是一个基于 Flexbox 的免费开放源代码 CSS 框架。

看了几天的 Vue 教程，所以打算用 Vue 重写一下前端页面，再配合 Vue 的 UI 框架，网页就做好啦！ ![效果展示](https://i.loli.net/2020/02/02/XAhTUiVMbu1LEPf.jpg " ") 不过只能说这个页面是基于 Vue 做的，因为我是用 uni-app 开发完成的。 纯 Vue 来说，一些用 JavaScript 简单操作的页面还能写写，像这样交互太多的（比如说上拉加载，下拉刷新），有工具还是安心用工具写吧。自己折腾太浪费时间。

# uni-app

说起 uni-app，前端朋友应该不陌生，但是对于后端来说，可能是第一次听说，我也是在之前开发微信小程序的时候考虑到，有没有一套代码能支持微信小程序，支付宝小程序等等一些类似的平台，没想到还真的有，还不止 uni-app 一款，但是因为 uni-app 支持 Vue，我认为上手应该会更快一些，所以就没考虑其他的。

> [uni-app](https://uniapp.dcloud.io/) 是一个使用 Vue.js 开发所有前端应用的框架，开发者编写一套代码，可发布到iOS、Android、H5、以及各种小程序（微信/支付宝/百度/头条/QQ/钉钉）等多个平台。

可以说有了 uni-app，开发 H5 是真的方便了，就类似写微信小程序一样，很多常用的接口已经写好，不用自己动手写了，大大提升开发效率。

# 使用记录

说这么多废话，为什么还要写文章记录呢？ 因为这个 uni-app 用起来，没有微信小程序用起来顺手，可能是开始就是用微信小程序的缘故，也可能是对 Vue 语法非常不熟悉的缘故（肯定是第二种），导致开发起来有些操作不是很熟练。所以写一篇文章记录下来，日后再次开发的时候还有迹可循。

## 引入 ColorUI

下载下来 ColorUI 的文件，放入项目根目录（和 pages 同级），然后在 App.vue 引入。

```markup
<style>
@import 'colorui/main.css';
@import 'colorui/icon.css';
</style>
```

## 使用全局变量

在 App.vue 中，参考下列代码。

```markup
<script>
export default {
    globalData: {
        DB_URL: 'http://127.0.0.1:5000/'
    },
    onLaunch: function() {},
    onShow: function() {},
    onHide: function() {}
};
</script>
```

其中 `globalData` 就是保存全局变量的位置，我们可以存入一些公共变量。 使用：

```javascript
let _url = getApp().globalData.DB_URL
```

## manifest.json

这个文件管理了本次开发的大部分配置，首先需要改动的就是**路由模式**。

```javascript
"h5": {
        "template": "template.h5.html",
        "router": {
            "mode": "history"
        }
    }
```

如果使用 hash 模式，url 会有一个 `#`，看起来十分别扭。

## pages.json

首次开发，一定会看到顶部 uni-app 的标题栏，如何去除呢？

```javascript
{
    "path": "pages/tabbar/index/index",
    "style": {
        "navigationBarTitleText": "uni-app"
        "navigationStyle": "custom",
        "app-plus": {
            "titleNView": false
        }
    }
}
```

这样就去掉了。

## template 部分

### 通过首页标题进入详情页

```markup
<view class="card" @tap="getDetail(item.question_id)">{{ item.title }}</view>
```

这里本来想用类似小程序的 `data-*` 传值，但是不清楚是代码的问题，还是 uni-app 有 bug，一直没生效。 网上虽然有提到 uni-app 在 `data-*` 这部分有过 bug 的记录，不过应该是已经修复了，不清楚为什么不能使用，只好曲线救国，在运行函数的时候，作为函数的参数传入吧。

### 动态传入 style

使用 ColorUI 框架，在处理用户头像这块使用的是 `background-image:url()`。如果直接 `:style` 一定会报错，所以正确的使用方式如下：

```markup
<view class="cu-avatar round lg" :style="{ 'background-image': 'url(' + item.avatar_url + ')' }"></view>
```

使用单花括号，以字典的形式传参。这里前端的朋友肯定一眼就明白了。css 真的是我的知识盲区啊

### 循环展示图片

```markup
<view v-for="pic in item.photo_list" v-bind:key="pic.index">
    <image mode="widthFix" :src="pic"></image>
</view>
```

这里的 `src` 也与小程序不同，直接使用 `Lsrc="pic"` 即可。