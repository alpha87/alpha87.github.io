---
title: Loon 配置文件分享
tags:
  - Loon
  - 科学上网
id: '209'
categories:
  - - 好物推荐
date: 2020-02-18 15:36:59
---

![bg](https://i.loli.net/2020/02/18/grvkt1eENAOd6DS.jpg)

这是一份非官方的个人配置，和之前的 [QuantumultX 配置](https://lijianxun.top/106.html)类似，因为没有复杂的需求，所以没有复杂的策略组。尽量做到简单实用。如果你和我有着同样的需求，可以继续阅读。如果你是小白想入门使用 Loon，可以继续阅读。
<!-- more -->
这份配置融合了[官方推荐配置文件](https://www.notion.so/aea3efeb1b1e4b38b258c626df09c548)，在保证只有一组策略的情况下，使用了 NobyDa，ACL4SSR 和神机规则。实现国内直连，国外扶梯，去除广告的效果。

**需要注意的是，这是一份商店版的配置文件，如果需要使用脚本功能，这份配置暂时不支持，但该配置文件会随 Loon 商店版本同步更新，请持续关注。**

# Loon

![界面](https://i.loli.net/2020/02/18/Tvgy5YaOofMxCn4.jpg)

界面美观，很好看。首页的仪表盘（快捷方式）几乎包含了所有功能选项。当然这些选项都是可以自定义展示与否。但是对不喜欢折腾这类软件，只想达到上网去广告的目的的同学来说可能有些不友好。所以我个人整理了这份简单配置，什么麻烦看不懂的配置统统走开，我只想简单快速上网！

# 使用

### 1 - 复制配置文件 URL

[](https://raw.githubusercontent.com/alpha87/Loon-Profiles/master/LoonConfig.yaml)[https://raw.githubusercontent.com/alpha87/Loon-Profiles/master/LoonConfig.yaml](https://raw.githubusercontent.com/alpha87/Loon-Profiles/master/LoonConfig.yaml)

### 2 - 在配置中选择 **从 URL 下载**

![下载配置文件](https://i.loli.net/2020/02/18/uG51OST6wokjDyp.jpg)

### 3 - 修改订阅链接

![修改订阅链接](https://i.loli.net/2020/02/18/B9qSmd3caOWfueK.jpg)

左划点击 编辑，将示例订阅链接改为你的订阅链接。

由于 Loon 配置文件的机制，必须存在一组策略组，所以如果你只有一个机场，这样修改完全满足需要。如果你有多个订阅链接，需要修改配置文件的这部分：

```bash
[Remote Proxy]
Subs0 = https://example/server-complete.txt
Subs1 = https://example/server-complete.txt

[Proxy Group]
Default = select, Subs0, Subs1
```

其中 Subs 是订阅链接的名称，并且需要在 `[Proxy Group]` 的 Default 中增加该名称，这样就保证策略组使用了所有的订阅节点。

### 4 - 开启 MITM（可选）

![开启 MITM](https://i.loli.net/2020/02/18/PbJEeA1D5lIOw8y.jpg)

如果你是从 QuantumultX 过来的用户应该知道这个选项。如果你是一名新用户，简单理解使用这个功能会提升去广告的能力。当然如果之后配合脚本还会有其他功效。

这样配置完成后，就可以直接开始使用了。

你的浏览器不支持播放此视频！

# 进阶教程

这份配置只是帮助你快速使用 Loon，我们还能根据节点的响应速度自动选择节点，根据 WIFI 名称，蜂窝网络等选择节点或策略组等等。当然了，Loon 同样有一份详细教程。

更多用法还请参考 [Loon 不完全教程](https://www.notion.so/Loon-f0a98c39f5224c09b281c79837380431)。

感谢阅读。