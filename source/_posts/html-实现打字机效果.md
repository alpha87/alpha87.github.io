---
title: HTML 实现打字机效果
tags:
  - JavaScript
id: '97'
categories:
  - - 前端
date: 2020-01-15 11:59:04
---

![](https://cdn.pixabay.com/photo/2016/01/30/22/04/typewriter-1170657__480.jpg) 怎样让博客的标语更与众不同呢？ 可以使用打字机效果，让标语一字一字打出来。这样看起来更有动感。
<!-- more -->
这里我们需要用到 [typed.js](https://github.com/mattboldt/typed.js/)。

# 安装

```bash
npm install typed.js

yarn add typed.js

bower install typed.js
```

# CDN

```markup
<script src="https://cdn.jsdelivr.net/npm/typed.js@2.0.11"></script>
```

# 使用

```markup
<html>

  <head>
    <title>TypedJS Test</title>
    <script src="https://cdn.jsdelivr.net/npm/typed.js@2.0.11"></script>
  </head>

  <body>
    <span class="test"></span>
    <script>var options = {
        strings: ['天行健，君子以自强不息。'],
        typeSpeed: 40
      };

      var typed = new Typed('.test', options);
    </script>
  </body>

</html>
```

# 官方示例

可以参考这个网页的效果：[http://mattboldt.github.io/typed.js/](http://mattboldt.github.io/typed.js/)