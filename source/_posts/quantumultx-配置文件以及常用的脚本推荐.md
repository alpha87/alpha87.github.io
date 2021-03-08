---
title: QuantumultX 配置文件以及常用的脚本推荐
tags:
  - QuantumultX
  - 科学上网
  - 脚本
id: '106'
categories:
  - - 好物推荐
date: 2020-01-27 12:08:48
---

这一段时间把科学上网工具（iOS 平台）从小火箭换成了 QuantumultX，因为不论是颜值，性能，还是自定义配置文件，QuantumultX 都完胜小火箭。当然小火箭的更新速度和协议支持是大部分工具无法比拟的。

所以就把我自己使用的极简策略和常用脚本（去广告脚本，签到脚本）推荐出来，小白可以参考一下。在参考以后，配合默认配置文件估计就可以很快上手更新自己独有的策略。
<!-- more -->
# 配置文件

```bash
# - QuantumultX CONFIG - #

[general]
server_check_url=http://www.qualcomm.cn/generate_204
dns_exclusion_list=*.cmpassport.com, *.jegotrip.com.cn, *.icitymobile.mobi, *.pingan.com.cn, *.cmbchina.com
geo_location_checker=http://ip-api.com/json/?lang=zh-CN, https://raw.githubusercontent.com/KOP-XIAO/QuantumultX/master/Scripts/IP_API.js

[dns]
server=223.5.5.5

[policy]

[server_remote]

[filter_remote]
https://raw.githubusercontent.com/limbopro/Profiles/master/limbopro/QuantumultX.list, tag=机场域名, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/Global.list, tag=国外路线, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/ForeignMedia.list, tag=国外视频, force-policy=proxy, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/Media/YouTube.list, tag=YouTube, force-policy=proxy, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/DomesticMedia.list, tag=国内视频, force-policy=direct, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/China.list, tag=国内路线, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/Hijacking.list, tag=运营商劫持及恶意网站, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/Advertising.list, tag=去广告, enabled=true
https://raw.githubusercontent.com/ConnersHua/Profiles/master/Quantumult/X/Filter/Unbreak.list, tag=UNBREAK, enabled=true
https://raw.githubusercontent.com/NobyDa/Script/master/QuantumultX/AdRule.list, tag=NobyDa去广告, enabled=true

[rewrite_remote]
https://raw.githubusercontent.com/NobyDa/Script/master/QuantumultX/Rewrite_lhie1.conf, tag=默认, enabled=true

[server_local]

[filter_local]
ip-cidr, 10.0.0.0/8, direct
ip-cidr, 127.0.0.0/8, direct
ip-cidr, 172.16.0.0/12, direct
ip-cidr, 192.168.0.0/16, direct
ip-cidr, 224.0.0.0/24, direct
geoip, cn, direct
final, proxy

[rewrite_local]
; YouTube 去广告
^https?:\/\/.+\.googlevideo\.com\/.+&amp;oad url reject-img
^https?:\/\/.+\.googlevideo\.com\/.+ctier url reject-img
^https?:\/\/youtubei\.googleapis\.com\/youtubei\/.+ad_ url reject-img
^https?:\/\/youtubei\.googleapis\.com\/youtubei\/.+log_ url reject-img
^https?:\/\/.+\.youtube\.com\/get_midroll_ url reject-img
^https?:\/\/premiumyva\.appspot\.com\/vmclickstoadvertisersite url reject-img
^https?:\/\/.+\.youtube\.com\/api\/stats\/ads url reject-img
^https?:\/\/.+\.youtube\.com\/api\/stats\/.+adformat url reject-img
^https?:\/\/.+\.youtube\.com\/pagead\/ url reject-img
^https?:\/\/.+\.youtube\.com\/ptracking url reject-img

; 去除微信公众号文章内广告
^https?:\/\/mp\.weixin\.qq\.com\/mp\/getappmsgad url script-response-body WeChat.js

; 去除 IT 之家新闻列表广告
^https?:\/\/api\.ithome\.com\/json\/slide\/index url script-response-body ITHome.js
^https?:\/\/api\.ithome\.com\/json\/newslist\/news url script-response-body ITHome.js
^https?:\/\/api\.ithome\.com\/json\/listpage\/news.* url script-response-body ITHome.js

; PriceTag 去首页广告
^https?:\/\/appfan\.im\/api\/v2\/topics\/.*?/posts.* url script-response-body PriceTagAD.js

; 大姨妈去广告
^https?:\/\/ssp-x\.yoloho\.com\/api\/ad.* url script-response-body Dayima.js

[mitm]
hostname=mp.weixin.qq.com, api.ithome.com, appfan.im, ssp-x.yoloho.com, *.googlevideo.com, s.youtube.com, www.youtube.com, youtubei.googleapis.com
```

**GitHub 地址：[https://raw.githubusercontent.com/alpha87/QuantumultX-Profiles/master/quantumultX\_profile.conf](https://raw.githubusercontent.com/alpha87/QuantumultX-Profiles/master/quantumultX_profile.conf)**

因为我不看 netflix 等，对不同的应用或域名没有特殊节点需求，所以这个配置文件极为简单，没有任何策略。

这份配置文件已经屏蔽了大部分广告，我又根据自己的需求屏蔽了 YouTube 五秒广告，微信公众号广告，IT 之家新闻列表广告，大姨妈烦人广告。

**去广告脚本地址：[https://github.com/alpha87/QuantumultX-Profiles](https://github.com/alpha87/QuantumultX-Profiles)**

使用效果类似小火箭，国内直连，国外扶梯，广告拦截。只要开启就可以无缝上网。

效果图如下：

![首页](https://i.loli.net/2020/01/27/Bz9cn6QOeNbrxDo.jpg)

# 去广告脚本

去除广告理解起来很简单。可以分为两种去广告方式：

1.  直接拒绝连接，reject；
2.  修改 response

以微信公众号广告为例，我们使用第二种方式，修改响应，去掉广告部分即可。

使用脚本的话需要在脚本头部注释填写你的设备 ID。设备 ID 在其他设置里可以找到：

![设备 ID](https://i.loli.net/2020/01/27/4Jpwm2SH3xezQYf.jpg)

```javascript
/**
 * @supported 你的设备 ID
 */

// 去除微信公众号文章内广告
var obj = JSON.parse($response.body);
obj.advertisement_num = 0;
obj.advertisement_info = [];
delete obj.appid;
$done({ body: JSON.stringify(obj) });
```

这部分代码很好理解。`obj` 就是微信公众号返回的数据，`obj` 里的 `appid`，`advertisement_num` 和 `advertisement_info` 都是和广告有关的数据。如何判断 `advertisement_num` 和 `advertisement_info` 分别给 `0` 和 `[]` 呢？我们只需要参考一个没有广告的公众号会发现这个请求还会存在，而且返回数据对应正是 `0` 和 `[]`。没有广告的响应也没有 `appid` 字段，所以我们也要删掉。这段代码就是这个意思，看不懂的其他部分是 JavaScript 的语法和一些格式转换。

使用脚本去广告就做到了微信公众号成功请求了广告，但是我们客户端这边不做展示，是不是比直接拒绝请求要更高级一些？

大部分可以修改响应的脚本基本都是这个原理，所以你只要稍稍懂一点网络相关知识，就可以抓包来定义自己的去广告脚本。

# 签到脚本

定时签到对 QuantumultX 的版本有一定的要求，理论上要大于 v1.0.5。

开启定时执行脚本，QuantumultX 称之为构造请求：

![构造请求](https://i.loli.net/2020/01/27/mOx4J5oBakqzisY.jpg)

构造请求的时候需要用到 cron 定时任务相关知识：

```bash
*    *    *    *    *    
-    -    -    -    -    
|    |    |    |    |    
|    |    |    |    +----- 星期 (0 - 7)
|    |    |    +---------- 月份 (1 - 12)
|    |    +--------------- 日期 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

签到一般都是每日签到，所以我们应该只会用到前两位。

这里举几个常用例子：

```bash
0 0 * * * 每日0点执行

10 0 * * * 每日0点10分执行

0 9 * * * 每日9点执行

0 21 * * * 每日21点执行

0 9-21/3 * * * 每日9点-21点，每隔三小时执行（一般用于天气脚本定时推送）
```

其他例子：

```bash
0 0 * * 6 每周六0点执行

0 0 * * 0 每周日0点执行

0 0 15 * * 每月15日执行

0 0 15 3 * 每年3月15日执行
```

如果你还想自定义定时任务，可以使用这个工具来检查 cron 语法是否存在问题：[https://tool.lu/crontab/](https://tool.lu/crontab/)