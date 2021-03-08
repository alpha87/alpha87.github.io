---
title: 使用 Python 抓取知乎美图
tags:
  - Python
  - 爬虫
  - 脚本
  - 钓鱼贴
id: '59'
categories:
  - - 编程学习
date: 2019-07-01 10:49:28
---

![](https://cdn.pixabay.com/photo/2018/06/03/01/15/fish-3449604__480.png)

之前写到**宅宅生活收藏夹**的部署方法，见 [使用Flask，Nginx，Gunicorn，Supervisor完成网站部署](https://lijianxun.top/?p=52)。这次介绍一下如何抓取知乎答案，获取知乎美图。

可以说爬虫是学习 Python 的入门必修课。当能独立写出第一个完整的爬虫的时候，我们已经迈出了一大步。因为在这过程中，我们已经学会了如何查看文档，学会使用 Python 相关库的操作，怎样使用 Chrome 的开发者工具（相关工具）和把抓取的数据保存到数据库中等等一系列操作，当然收获最多的还是学习 Python 的自信心。

下面以抓取知乎图片为例，迈出我们自学 Python 爬虫的第一步。
<!-- more -->
本文除 Python 相关库的使用外，还会涉及到 `mongo` 数据库的使用。

因为**宅宅生活收藏夹**只是为了收集知乎钓鱼贴的图片，有针对性，所以不能通过获取知乎首页列表全面抓取。当然抓取方式大同小异，把抓取列表改为知乎首页也是可以的。

# 整体思路

首先我们需要收集知乎各类钓鱼贴，这一步可以使用爬虫（通过爬取别人已经收集的钓鱼贴，或者各种社区可能已有钓鱼贴专栏等）获取，也可以在浏览知乎时，发现合适的贴子手动添加。

我们需要将钓鱼贴的 ID 存入数据库中，爬虫运行时读库获取需要爬取的目标贴。

通过写一个爬虫，生成任务池多线程调用。将获取到的答案数据清洗，只收集答案中的图片。

存入库中的结构和知乎的类似：答案集合保存所有答案，每一个答案是一条独立的文档。可能写的有点绕。

具体结构类似这种结构：

```javascript
{
    "Answer": [
        {
            "id": 1,
            "question_id": "xxx",
            "title": "xxx",
            "author": "xxx",
            "content: [
                "imgUrl",
                "..."
            ]
        },
        {
            "id": 2,
            "question_id": "xxx",
            "title": "xxx",
            "author": "xxx",
            "content: [
                "imgUrl",
                "..."
            ]
        },
        ...
    ]
}
```

其中 `Answer` 为数据库的集合，集合中的每一项是一条回答。

用这种格式保存的好处是我们只需通过贴子的 ID 查询答案即可，保存起来也非常方便。

# 知乎爬虫

## 开始之前

在开始之前需要安装项目依赖，只用到两个常用库：

`python3 -m pip install requests pymongo`

分别用来请求 URL 和操作数据库。

安装完成后记得启动 `mongo` 服务。

## Spider

爬虫代码比较简单，代码不过百行，关键找到知乎答案的接口，解析即可。而且这个接口也是非常好找的。

用 Chrome 的开发者工具一下就找到了...

![知乎 API 截图](https://i.loli.net/2020/02/14/gFpQvUTA2WhwJa9.png)

接口也没有任何加密或权限限制，在请求头中加入 `Cookies` 就可以了。是不是突然觉得很简单？

只要我们控制好频率，不要影响到知乎服务就行了。毕竟我们只是想获取答案而已。

通过 Chrome 开发者工具分析，请求携带了如下参数，我们只用到 `limit` 和 `offset`，用来控制接口返回的数量和返回的位置。

```
include: data[*].is_normal,admin_closed_comment,reward_info,is_collapsed,annotation_action,annotation_detail,collapse_reason,is_sticky,collapsed_by,suggest_edit,comment_count,can_comment,content,editable_content,voteup_count,reshipment_settings,comment_permission,created_time,updated_time,review_info,relevant_info,question,excerpt,relationship.is_authorized,is_author,voting,is_thanked,is_nothelp,is_labeled,is_recognized,paid_info;data[*].mark_infos[*].url;data[*].author.follower_count,badge[*].topics
offset:
limit: 3
sort_by: default
platform: desktop
```

完整的请求 URL 是：

`https://www.zhihu.com/api/v4/questions/21115811/answers?include=data%5B*%5D.is_normal%2Cadmin_closed_comment%2Creward_info%2Cis_collapsed%2Cannotation_action%2Cannotation_detail%2Ccollapse_reason%2Cis_sticky%2Ccollapsed_by%2Csuggest_edit%2Ccomment_count%2Ccan_comment%2Ccontent%2Ceditable_content%2Cvoteup_count%2Creshipment_settings%2Ccomment_permission%2Ccreated_time%2Cupdated_time%2Creview_info%2Crelevant_info%2Cquestion%2Cexcerpt%2Crelationship.is_authorized%2Cis_author%2Cvoting%2Cis_thanked%2Cis_nothelp%2Cis_labeled%2Cis_recognized%2Cpaid_info%3Bdata%5B*%5D.mark_infos%5B*%5D.url%3Bdata%5B*%5D.author.follower_count%2Cbadge%5B*%5D.topics&offset=&limit=3&sort_by=default&platform=desktop`

我们只要动态更改其中的 `question`，`limit` 和 `offset` 就可以了。

我们通过接口返回的答案总数，判断需要翻多少页，当然也可以通过接口返回的 `next` 和 `previous` 来获取下一页或前一页答案链接。知乎的接口设计的非常方便啊。

当然在翻页抓取的时候切记设置睡眠时间，放在服务器上爬的慢一点也没关系。**不要影响到知乎的正常服务**。

请求成功后我们就可以根据自己的需求，存储数据了，至于如何判断答案中是否有图片，可以参考以下代码。

使用到了 `lxml` 库，也可以使用 `re` 库代替。

```python
# 部分代码

@staticmethod
def parse_content(content):
    """解析答案中的 content，直接获取图片"""

    if "<img " in content:
        img_list = set(etree.HTML(content).xpath("//img/@data-original"))
        return list(img_list)
    else:
        return []
```

先判断回答中是否有 `img` 标签，如果没有直接返回空列表，如果有的话，通过 `data-original` 属性获取原始大小的图片链接。也是返回一个包含图片链接的列表类型。

在入库的时候，我们通过 `parse_content` 的返回判断是否需要入库，如果是 `[]` 就跳过，如果列表不为空就入库。这样在之后展示的时候不会只展示作者信息，却没有回答的情况了（其实是该作者回答中没有图片）。

## 调用爬虫

当我们完成上述操作，就可以单独写一个文件使用多线程调用爬虫了。

```python
from concurrent.futures import ThreadPoolExecutor

qid_list = db.get_all_question()
crawler_list = [ZhihuSpider(qid).run for qid in qid_list]

with ThreadPoolExecutor(max_workers=4) as executor:
    future = [executor.submit(task) for task in crawler_list]
    for f in future:
        f.result()
```

`qid_list` 来自查库获取所有的知乎贴子 ID。

使用 `concurrent.futures` 库并行执行任务，在我们的代码里使用 `ThreadPoolExecutor`，它使用线程池来异步执行调用。

`max_workers` 控制最多线程的使用，本例中使用最多4个线程执行任务。

具体文档见 [ThreadPoolExecutor](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#threadpoolexecutor)，是对 `thread` 库的封装，让我们使用线程时更加简单。

完整代码见 [github](https://github.com/alpha87/zhihuFish)。

最后总结一下整体思路：我们先把需要抓取的 ID 存入数据库，作为任务池，爬虫调取任务池中的 ID，将爬取结果再保存到数据库。

等我们学会了 Python 爬虫，再学 Web 开发的时候，还能将我们抓取的结果展示出来。一不小心又学习了 Python Web 开发，多好。

# 福利

当我们爬虫写好，入库，并成功展示出来，不知不觉就实现了一个小程序项目：**宅宅生活收藏夹**。

![小程序首页展示](https://user-images.githubusercontent.com/25655581/60482994-3c431e00-9cc6-11e9-9344-3c50b9dbef10.png)