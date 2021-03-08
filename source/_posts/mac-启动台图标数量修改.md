---
title: Mac 启动台图标数量修改
tags:
  - Mac
id: '99'
categories:
  - - 贵在折腾
date: 2019-02-01 12:00:10
---

![](https://i.loli.net/2020/02/06/D8X6pTWqQCzK12H.png)

默认启动台的图标太大，根本放不了几个app，如何修改显示数量呢？

当然启动应用也可以使用聚焦搜索。
<!-- more -->
**`?`代表数量**

调整行图标数量：

`defaults write com.apple.dock springboard-rows -int ?`

调整列图标数量：

`defaults write com.apple.dock springboard-columns -int ?`

修改后使其生效，输入命令：

`defaults write com.apple.dock ResetLaunchPad -bool TRUE;killall Dock`