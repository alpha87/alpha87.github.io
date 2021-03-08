---
title: 博客主题自定义修改记录
tags:
  - JavaScript
  - wordpress
id: '312'
categories:
  - - 贵在折腾
date: 2020-03-08 17:24:04
---

本博客用的是 Kratos 主题。但是某些地方个人做了改动。

因为对 PHP 不了解，所以折腾完之后写此文记录一下。
<!-- more -->
# 底部添加运行时间

我们经常能看到他人的博客页脚的位置有各式各样的建站时间显示。核心还是使用 JavaScript 计算日期，并显示到页面上。

我们需要在 Wordpress 仪表盘，找到外观，选择主题编辑器，选择**主题页脚（footer.php）**文件，添加如下代码即可。

```markup
<div class="k-footer">
<script>
    var now = new Date();
    function createtime() {
      var grt = new Date("11/16/2016 00:00:00"); // 这里是你的建站时间
      now.setTime(now.getTime() + 250);
      days = (now - grt) / 1000 / 60 / 60 / 24;
      dnum = Math.floor(days);
      hours = (now - grt) / 1000 / 60 / 60 - 24 * dnum;
      hnum = Math.floor(hours);
      if (String(hnum).length == 1) {
        hnum = "0" + hnum;
      }
      minutes = (now - grt) / 1000 / 60 - 24 * 60 * dnum - 60 * hnum;
      mnum = Math.floor(minutes);
      if (String(mnum).length == 1) {
        mnum = "0" + mnum;
      }
      seconds =
        (now - grt) / 1000 - 24 * 60 * 60 * dnum - 60 * 60 * hnum - 60 * mnum;
      snum = Math.round(seconds);
      if (String(snum).length == 1) {
        snum = "0" + snum;
      }
      document.getElementById("timeDate").innerHTML =
        "本站已运行 " + dnum + " 天 ";
      document.getElementById("times").innerHTML =
        hnum + " 小时 " + mnum + " 分 " + snum + " 秒";
    }
    setInterval("createtime()", 250);
  </script>

...

<p><span id="timeDate">正在载入天数...</span><span id="times">载入时分秒...</span></p>
</div>
```

我们先用 JavaScript 把时间计算出来，然后在通过 `document.getElementById("times").innerHTML` 方法渲染到页面上。`<p><span id="timeDate">正在载入天数...</span><span id="times">载入时分秒...</span></p>` 需要添加到页面合适的位置。

实际效果如下：

![运行时间](https://i.loli.net/2020/03/08/PlSeW5csbznkr6a.png)

# 网站标语（公告）

这部分需要修改**首页模板（index.php）**中的代码。

一开始我并不清楚需要修改哪个文件中的代码，因为测试了半天都没有符合自己的预期。后来我想到搜索关键字的时候会弹出类似的界面。所以直接在搜索部分添加了 `else` 语句。保证了平时显示标语或者公告，在搜索关键词的时候隐藏标语，看起来更美观一些。

搜索部分的代码修改：

```markup
<?php
        if ( is_search() ) { ?>
        <div class="article-panel">
            <div class="search-title"><?php _e('搜索内容：', 'kratos');the_search_query(); ?></div>
        </div>
        <?php } else { ?>
        <div class="article-panel">
                <span id="mySweetIcon">🍒</span><span id="loveForYou">春水初生，春林初盛，春风十里，不如你。</span>
            </div>
        </div>
<?php } ?>
```

![网站标语](https://i.loli.net/2020/03/08/sPMdHzmIpkuV89U.png)

# 鼠标滑过显示小标题

这部分是在看 Bootstarp4 的 Tooltip 看到的，就尝试了一下，因为 Kratos 主题就用到了 Bootstarp，所以添加这个功能很简单了。

在网站标语的基础上修改即可。

```markup
<div class="article-panel">
    <div class="shadow-sm p-3 bg-white font-weight-light text-wrap" data-toggle="tooltip" title="欢迎来访！如果觉得小站不错请推荐给您的朋友！">
        <span id="mySweetIcon">🍒</span><span id="loveForYou">春水初生，春林初盛，春风十里，不如你。</span>
    </div>
</div>
```

![小标题](https://i.loli.net/2020/03/08/Hh6AONzofJlyavg.png)

# 文章最后编辑时间提示

一开始想用 PHP 计算时间，渲染到页面上就行，但是因为对 PHP 非常不熟悉，试了试没有成功就放弃了。但是转念一想，这个不就和底部显示建站时间一样吗？用 JavaScript 计算时间渲染就可以了。但是文章的时间是用 PHP 的函数获取的，我该如何在 JavaScript 中传入 PHP 生成的参数呢？没想到非常简单无脑。

```javascript
<script>
var now = new Date();
function getCreateTime(dataTime) {
    var grt = new Date(dataTime);
    now.setTime(now.getTime() + 250);
    days = (now - grt) / 1000 / 60 / 60 / 24;
    dnum = Math.floor(days);
    document.getElementById("postDateL").innerHTML = dnum;
    if (dnum < 30) {
        document.getElementById("postDateTitle").style.display = 'none';
    }
}
</script>
```

```markup
<div class="callout callout-warning" id="postDateTitle">
  <p>此文距离最后一次编辑已经过去 <span id="postDateL">0</span> 天。</p>
</div>

<script>
    var v_arr="<?php echo the_modified_date( 'Y-m-d' );?>"; 
    getCreateTime(v_arr);
</script>
```

看到代码应该一下就明白了。

```javascript
var v_arr="<?php echo the_modified_date( 'Y-m-d' );?>"; 
```

附上 callout 的 css：

```css
.callout {
  padding: 10px;
  margin: 0 10px 10px;
  border: 1px solid #eee;
  border-left-width: 5px;
  border-left-color: crimson;
  border-radius: 3px;
}

.callout p {
  margin-bottom: auto;
}
```

![时间提示](https://i.loli.net/2020/03/08/SmY6DOJ2QdBbG5V.png)