---
title: 使用 QuantumultX 实现卡车极速自动签到
tags:
  - QuantumultX
  - 科学上网
  - 脚本
id: '61'
categories:
  - - 好物推荐
date: 2020-02-03 10:50:54
---

![](https://i.loli.net/2020/02/06/7QzBokESytGDKvq.jpg)

[卡车极速](http://m8.pw/mddv)每天都有签到，机械化的操作最好还是交给机器去执行。
<!-- more -->
# 使用前提

1.  QuantumultX（1.0.5版本及以上）
2.  卡车极速账户（其他机场平台应该类似）

# 脚本

## 获得 Cookie

```javascript
/**
 * @supported 设备 ID
 */

let headerCookie = $request.headers["Cookie"];

if (headerCookie) {
  if ($prefs.valueForKey("kcjsCookie") != undefined) {
    if ($prefs.valueForKey("kcjsCookie") != headerCookie) {
      var cookie = $prefs.setValueForKey(headerCookie, "kcjsCookie");
      if (!cookie) {
        $notify("更新卡车极速Cookie失败！", "", "");
      } else {
        $notify("更新卡车极速Cookie成功！", "", "");
      }
    }
  } else {
    let cookie = $prefs.setValueForKey(headerCookie, "kcjsCookie");
    if (!cookie) {
      $notify("首次写入卡车极速Cookie失败！", "", "");
    } else {
      $notify("首次写入卡车极速Cookie成功！", "", "");
    }
  }
}
$done({});
```

以上代码用来获得 Cookie。因为根据经验，这个平台的 Cookie 有效期只有七天。每周获取比较麻烦，所以通过脚本实现。

需要在配置文件填入以下部分配置：

```yaml
[rewrite_local]
; 卡车极速 Cookies 获取
^https?:\/\/www\.kcjisu\.casa\/user url script-request-header kcjsCookie.js

[mitm]
www.kcjisu.casa
```

## 签到脚本

```javascript
/**
 * @supported 设备 ID
 */

let Cookie = $prefs.valueForKey("kcjsCookie");

let Req = {
  url: "https://www.kcjisu.casa/user/checkin",
  method: "POST",
  headers: {
    Cookie: Cookie,
    Origin: "https://www.kcjisu.casa",
    Referer: "https://www.kcjisu.casa/user",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36"
  }
};

$task.fetch(Req).then(response => {
  try {
    let doc = JSON.parse(response.body);
    if (doc["ret"] == 1) {
      $notify(
        "卡车极速",
        "成功",
        `${doc["msg"]}\n已使用流量${doc["trafficInfo"]["lastUsedTraffic"]}\n剩余流量${doc["trafficInfo"]["unUsedTraffic"]}`
      );
    } else {
      $notify("卡车极速", "成功", doc["msg"]);
    }
  } catch (error) {
    $notify("卡车极速", "失败", error);
  }
});
```

以上代码的效果就是模拟浏览器请求签到接口。

配置定时任务：

```yaml
[task_local]
01 00 * * * kcjs.js
```

意思是每日零点零一分签到。

可能是 QuantumultX 的 bug，零点零分执行脚本不会有通知，但是仍会签到成功，所以推迟了一分钟。

[脚本地址见 github。](https://github.com/alpha87/QuantumultX-Profiles/tree/master/kcjs)

以上就是使用 QuantumultX 签到卡车极速的所有内容，快去试试吧！

## 七天后的测试结果

如果七天内或七天过后，卡车极速用户后台并未自动退出登录，需要手动退出登录，这样才会生成新的 Cookie。如果中途签到失败，也可以重新登录以获取最新 Cookie。