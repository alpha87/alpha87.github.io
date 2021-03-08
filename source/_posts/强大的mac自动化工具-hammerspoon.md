---
title: 强大的Mac自动化工具——Hammerspoon
tags:
  - Hammerspoon
  - 脚本
id: '42'
categories:
  - - 好物推荐
date: 2019-04-16 10:21:45
---

![](https://i.loli.net/2020/02/06/QwbV2p3WYxFKN5M.jpg)

前两天有幸看到[Hammerspoon](http://www.hammerspoon.org/)的介绍，可以说可玩性还是很大的，一个实用的脚本可以帮助我们节省不少时间，提升不少的工作效率。当然既然是脚本工具，就会有很多自定义的功能。简单了解Hammerspoon以后自己写了个小工具，用来在Mac菜单栏查看最新的少数派文章，点击后使用默认浏览器打开相关文章的链接。
<!-- more -->
本人还是很喜欢[少数派](https://sspai.com/)上的文章的，读起来舒服也感觉质量很高，虽然有时也有一些软文的存在，不过还是不影响其媒体整体对我的印象。

所以只要发现类似的工具就会拿来练手，实在是不好意思。虽然拿来练手，也是为了方便阅读其文章，也是合理。

之前有用JSBox做过一个[少数派阅读器](https://jsboxbbs.com/d/468--)，用起来还是很舒服的。

## Hammerspoon

其实不用介绍很多，如果能看到这篇文章，说明你已经知道它是用来干什么的了，简单理解就是使用Lua语言来编写一些脚本和系统API对接实现一些功能和操作。官网的入门demo比较好理解。使用组合键实现通知栏弹出hello，world。

## 少数派小脚本

实现效果如下，点击后弹出最新的文章标题，数量是可控制的，在点击标题后使用浏览器打开对应文章。

![](https://ws2.sinaimg.cn/large/006tNc79ly1g24mglbjcfj31c00u07wn.jpg)

可能是对Lua语言的不熟悉，在处理`table`的时候不是很得心应手，想了好半天终于解决了，不同文章标题只会使用最后一篇文章的url的问题。感觉应该还有更好的解决办法。

还有一个问题就是Mac菜单栏的logo，在网上找了很多少数派官网了logo都不合适，都是一片黑，实在没办法自己简单做了一个。

![](https://ws2.sinaimg.cn/large/006tNc79ly1g24m34ael8j30u00u0gnw.jpg)

## 代码部分

代码其实很简单，请求后展示，点击标题使用浏览器打开对应链接。

```lua
--[[
    少数派最新文章获取，点击后打开对应文章。
--]]

-- 请求文章数量
local article_num = "5"

-- 刷新时间（秒）
local s = 18000

local menubar = hs.menubar.new()
local title_items = {}
local articleKey = {}
local headers = {}
local articlesApi = "https://sspai.com/api/v1/articles?offset=0&limit=" .. article_num .. "&type=recommend_to_home&sort=recommend_to_home_at&include_total=false"
headers["User-Agent"] = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36"

function updateMenubar()
    menubar:setMenu(title_items)
end

function openArticle(url)
    hs.urlevent.openURL(url)
end

function getArticles()
    hs.http.doAsyncRequest(
        articlesApi, 
        "GET", 
        nil, 
        headers,
        function (code, body, htable)
            if code ~= 200 then
                hs.notify.new({title="SSPAI - Error", informativeText="无法获取到最新文章！"}):send()
                return 
            end
            raw_json = hs.json.decode(body)
            -- menubar:setTitle("少数派")
            menubar:setIcon(hs.image.imageFromPath("/Users/lijianxun/.hammerspoon/sspai/icon.png"):setSize({w=16,h=16}))
            title_items = {}

            for k, v in pairs(raw_json.list) do
                _title = v.title
                _url = "https://sspai.com/post/" .. v.id
                articleKey[k] = _url
                table.insert( title_items, {title = _title, fn = function() openArticle(articleKey[k]) end})
            end

            updateMenubar()
        end
    )
end

getArticles()
updateMenubar()
hs.timer.doEvery(s, getArticles)
```

这也是昨天看完lua教程后写的第一个脚本，还算满意。

github地址：[https://github.com/alpha87/.hammerspoon/tree/master/sspai](https://github.com/alpha87/.hammerspoon/tree/master/sspai)