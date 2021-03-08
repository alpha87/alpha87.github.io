---
title: 如何科学管理你的密码
tags:
  - 1Password
  - enpass
  - KeePass
  - 密码管理
id: '325'
categories:
  - - 好物推荐
date: 2020-03-17 19:35:23
---

对于密码管理这个话题，不同的人有不同的看法。一部分人可能认为密码管理并没有那么重要，为了方便记忆，几乎所有的账户都是一个相同的密码；另一部分人则会很重视密码管理，并且会养成良好习惯，长期坚持。我认为密码就相当于一道保险，在它没起作用的时候，你会觉得它可有可无，或者不值得花时间研究。可一旦出现问题，迎来的却是找回密码，等待申诉结果，可能所有相同密码的账户都被牵连等等问题。如果你之前从未考虑过密码管理问题，可能这篇文章会帮助到你。
<!-- more -->
# 弱密码的危害

不论我们是否管理密码，一定都清楚弱密码的危害，**容易被盗号**。

[2019 年最差密码排行榜](https://nordpass.com/blog/top-worst-passwords-2019/)公布，**12345** 名列榜首。榜单由安全公司 NordPass 发布。通过收集 2019 年数据泄露中暴露的密码（收集了近 5 亿个密码），列出了 200 种最流行的密码列表。

![受欢迎密码](https://i.loli.net/2020/03/17/ygkmEe87HRPD4hz.png)

如果这份榜单对你来说没有直观的感受，可以试试[卡巴斯基密码检测工具](https://password.kaspersky.com/cn/)，明确指出你的密码可以在多久之内被攻破。

例如我过去最常用的密码，几乎所有账号都是这个密码：

![旧密码](https://i.loli.net/2020/03/17/BpeKPS8xzNOVlhL.png)

普通的家庭电脑两天左右就可破解。

现在使用的密码，而且每个账号的密码都不相同：

![新密码](https://i.loli.net/2020/03/17/7DMobx8RZQI91Ty.png)

这样的感受是不是更直观一些。

# 开始管理你的密码

本文提供两种方式管理密码。一种是靠大脑记忆，另一种是通过使用软件协助管理。

通过大脑记忆是依靠规律来记忆密码。

通过软件管理密码基本都是一个思路：只需要记忆一个主密码，打开密码管理软件，之后的生成密码和输入都借助软件完成。

## 大脑记忆

人民日报曾在微博上提供过记忆密码的方法。

![人民日报](https://i.loli.net/2020/03/17/jyuKA59HWakLUzD.png)

举例来说，现在我需要给我的微信生成密码，公式可以是这样的（可以根据你的需求调换位置）：

**平台+生日+姓名+特殊字符**

那么生成密码将是：**weiXIN0807ljx!@#**

![微信密码](https://i.loli.net/2020/03/17/FfndVaYMzmhUAxS.png)

这套密码记忆方法可以保证不同的平台密码不同，同时还能加长密码长度。但是这种管理密码方法并不能保证在登录时自动输入。

如果你像我一样，不愿记忆密码，不愿输入密码，可以尝试密码管理软件。

## 选择密码管理器

先说说我个人在选择密码管理软件时的要求吧。

### 安全

首先我会考虑到的是安全问题。这个是最重要也是最基本的要求。作为一款密码管理软件，如果安全都谈不上，那就没有存在的必要了。好在只要是我们听说过的软件，基本都比较可靠。所以在挑选软件的时候选择知名度较高的就可以。

### 跨平台

这个需求同样很重要。毕竟现在的设备越来越多，如果说只能在手机或者在电脑上使用，那手动输入密码带来的痛苦会让你放弃密码管理。

我最常用的设备有 iPhone，Mac 和 Chrome 浏览器。所以只要密码管理软件同时满足这三个平台，且方便同步，我就会尝试。

### 价格

价格也是重要的指标。从我开始管理密码，一共尝试过三种密码管理软件，分别是 1 Password，enpass 和 KeePass。

[1 Password](https://1password.com/zh-cn/) 是老牌密码管理应用了，也是我接触最早的密码管理软件。我记得当时还是 68 元买断，但是在使用一年之后，1 Password 就改为订阅制，高额的费用让我放弃使用（因为我觉得我的密码不值得花那么多钱来管理）。

![1password价格](https://i.loli.net/2020/03/17/MJUHEaYFsSIBpuX.png)

不过最近 1 Password 的活动越来越多，基本上动动手指就能免费获得一年的使用权。如果想尝试，可以参考这篇文章：[使用 1Password 管理你的密码](https://lijianxun.top/48.html)。

[enpass](https://www.enpass.io/) 扬言自己坚持买断制，不会跟风改为订阅制。可没过多久，大概是 2019 年底改成了订阅制。其实不论是对 enpass 自己还是对用户，这样都是一种负责的做法，毕竟公司要恰饭。我们作为用户也不希望自己的密码随公司一起远去。enpass 价格还是很亲民的（毕竟不需要维护服务器）。如果你是 enpass 的老用户，之前付费会自动转为高级用户，即使改为订阅制，也相当于已经购买了终身版。可以说从买断制转为订阅制，这样的做法已经非常良心了，没有哪家公司敢这样承诺用户。 一度让我考虑当 1 Password 过期后更换成 enpass。即使现在，**如果有人让我推荐一款密码管理软件，我也会毫不犹豫的先建议使用 enpass**。

![enpass价格](https://i.loli.net/2020/03/17/bJYZFrVc9ohKMiA.png)

[KeePass](https://keepass.info/) 是我现在使用的密码管理应用，**重点是开源且免费**。不过从严格意义上来说，我现在使用的密码管理软件全部基于 KeePass。因为 KeePass 只有 Windows 客户端。但正是因为开源，所以出现了很多优秀的第三方客户端，满足各个平台的使用。

![keepass软件列表](https://i.loli.net/2020/03/17/H9fruU5ySIxbcqk.png)

### 外观

颜值的话 1 Password 没得说。不论是移动端还是桌面端，都非常大方美观。

![1p](https://i.loli.net/2020/03/17/ML2pTkDNhOaqIUy.png)

enpass 也不错。移动端和桌面端以蓝色为主色调。个人还是比较喜欢的。

![enp](https://i.loli.net/2020/03/17/19xlkDKpPLbqNWd.png)

KeePass 系列的密码管理软件，颜值是硬伤。可以说比较古代，像是上个世纪的产物，就不上图了。好在我对外观的要求不算太高，基本都能接受...

### 其他

KeePass 和上述两种密码管理软件不同，1 Password 将用户密码保存在自己的服务器上（所以费用很高，毕竟维护服务器需要成本）。 enpass 可以离线使用，也支持云同步，但是有自己的数据格式。KeePass 的逻辑是新建一个数据库，在数据库中保存密码。而这个数据库在用户手中。这也是我为什么从 enpass 转到 KeePass 的原因。密码管理软件不像其他软件，一旦选择基本上是长期使用，后期转移数据是非常麻烦的。虽然现在这类软件基本都支持导入和导出，可是如果一旦密码数量太多，在导入的过程中，某些字段稍微有一点误差，就会在后期维护造成不少麻烦。

因为我考虑到 KeePass 是开源应用，会有很多第三方软件支持，就算某个软件不能用了，但是只要支持 KeePass 数据库，我就可以继续使用密码。而且考虑到未来用户会越来越重视密码管理，基于 KeePass 的密码管理器会越来越多。所以我最终选择了 KeePass。

# iOS 平台

iOS 平台我使用过两款应用，一款是 FantasyPass，另一款是 KeePassium。

![两款应用](https://i.loli.net/2020/03/17/Jf5o98yKEuItgCe.jpg)

最终选择了 FantasyPass。不论是外观还是操作，细节等各方面都比 KeePassium 好用。具体可查看作者 [README](https://github.com/kaich/FantasyPass)。

FantasyPass 支持 WebDAV，可以使用坚果云同步数据库。如果你像我一样选择了 KeePass，可以试试这款软件。

# Mac 平台

Mac 上我试过三款都是开源应用。GitHub 上搜索 KeePass 就能找到。

![github-keepass](https://i.loli.net/2020/03/17/HWSbxtqencaoCEP.png)

它们分别是 [keeweb](https://keeweb.info/)，[keepassxc](https://keepassxc.org/) 和 [MacPass](https://macpassapp.org/)。

其中 keeweb 因为是基于 Electron 开发的，所以颜值最高。

![keeweb11](https://i.loli.net/2020/03/17/RAkgs3Bd24hNX6L.png)

其余两款，单单通过颜值判断，都像是上世纪的产物。可能 MacPass 还好一点？

keepassxc：

![keepassx](https://i.loli.net/2020/03/17/TJ8Y2mFRhfsgeku.png)

MacPass：

![macPass](https://i.loli.net/2020/03/17/9ZKq8ML7FeWAzo6.png)

其实这三款我还是很难选择的。毕竟 keeweb 比较符合现代人的审美，但是在细节上不如 keepassxc 和 MacPass。因为我的数据库使用了密码和密钥文件，keeweb 每次解锁的时候都要手动选择密钥文件，让我觉得很不方便。keepassxc 和 MacPass 都可以记录密钥文件的位置。所以 keeweb 首先被我排除了，如果你的数据库不需要密钥文件解锁，那我建议你使用 keeweb。

在 keepassxc 和 MacPass 之间我更偏向于 keepassxc。密码管理软件一方面要保证管理密码简单易操作，另一方面也要保证方便输入。

keepassxc 提供了 keepassxc-browser 配套使用。因为在 Mac 上，大部分输入密码的操作都是在 Chrome 浏览器上进行的，所以我会更多考虑浏览器的自动输入。如果 Chrome 没有保存网页的账户和密码，我会使用插件自动键入。感觉插件相比全局键入，匹配会更智能一些。

安装好 keepassxc-browser 插件后，要保证 keepassxc 处于运行状态，插件会自动检测 keepassxc 的数据库。

![插件](https://i.loli.net/2020/03/17/c2ZiBoYInKevDyj.png)

连接无误后，在登录的时候会看到一把绿色的钥匙，正是 keepassxc 的 logo。

![github登录](https://i.loli.net/2020/03/17/SjpGAFRr8YbfZId.png)

点击钥匙图标，密码就会自动填入了（如果你是第一次在此网站使用自动填入功能，keepassxc 会弹出提示让你先确认）。如果在登录框没有绿色钥匙，且插件运行正常，还能手动指定输入框的位置，这个功能还是非常实用的。

如果你不喜欢 keepassxc，更偏向于 keeweb 或是 MacPass，这里推荐一款 Chrome 插件：[KeePass Tusk](https://chrome.google.com/webstore/detail/keepass-tusk-password-acc/fmhmiaejopepamlcjkncpgpdjichnecm)。支持自动键入，支持 WebDAV，同步起来方便些。

# 总结

如果你不想折腾，也不在乎付费，可以试试 enpass，也可以薅羊毛使用一年的 1 Password。

如果想让数据掌握在自己手中，可以试试开源免费的 KeePass。不愿过多折腾，可以直接使用我推荐的应用，当然也可以自己尝试其他第三方 KeePass 密码管理器，适合自己的才是最好的。

感谢阅读。