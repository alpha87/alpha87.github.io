---
title: 一行代码搞定 Quantumult X 远程脚本
tags:
  - QuantumultX
  - 脚本
id: '339'
categories:
  - - 贵在折腾
date: 2020-03-29 09:28:27
---

因为各种各样的原因，Quantumult X 在 1.0.3 版本后取消了远程调用 JavaScript 代码的功能。今天我们要在最新的商店版（v1.0.6-build 212）中，使用一行代码来执行远程 JavaScript 代码，效果和本地一样。
<!-- more -->
# eval()

这是我们可以使用远程脚本的关键函数。 `eval()` 函数会将传入的字符串当做 JavaScript 代码进行执行。也就是说，我们只要请求远程脚本的 GitHub 地址，将返回的远程脚本执行即可。

# 举个栗子🌰

拿[京东签到脚本](https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js)来举例。 用一行代码执行京东签到是有前提条件的，要保证 Quantumult X 已经获取了京东的 Cookies。我们这里先做远程签到的展示。 以下是远程签到的全部代码，我们命名为 JD\_eval.js：

```javascript
$task.fetch({ url: "https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js" }).then(response => {eval(response.body);});
```

只有一行代码，完成了两件事。

1.  请求京东签到的远程代码地址；
2.  使用 `eval()` 函数执行请求的代码。

JD\_eval.js 和本地 JavaScript 脚本一样，定时执行就可以。 以下是我的测试结果，因为当天已经签到，所以大部分都显示已签过。 ![测试结果](https://i.loli.net/2020/03/29/id3LWAG2hxRDCJp.jpg) 其他脚本也是可以这么使用的。

# 推荐

如果看了上述文章有自己的想法，可以把那一行代码拓展一下，加一些异常处理和日志输出。如果是小白的话，推荐使用 yichahucha 大佬的脚本：[eval\_script.js](https://github.com/yichahucha/surge/blob/2.0/eval_script.js)。也能达到远程执行的目的。 👉 小白一键配置：https://github.com/alpha87/QuantumultX-Profiles/raw/master/quantumultX\_profile.conf 👉 GitHub 地址：[https://github.com/alpha87/QuantumultX-Profiles](https://github.com/alpha87/QuantumultX-Profiles) 如果想要使用远程脚本，快去尝试一下吧。