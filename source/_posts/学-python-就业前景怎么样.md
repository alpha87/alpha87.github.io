---
title: 学 Python 就业前景怎么样
tags:
  - Python
  - 数据分析
  - 爬虫
id: '78'
categories:
  - - 编程学习
date: 2017-04-21 09:34:36
---

![](https://cdn.pixabay.com/photo/2016/06/13/09/57/meeting-1453895__480.png) 学了python之后能找到工作吗？相信很多小伙伴都有过这个困扰，刚入门的我，作为新手练习，那我们今天就用python来分析一下，学了python以后能找到工作吗？能找到什么样的工作。
<!-- more -->
## 抓取信息

分析数据的前提是有数据。 这次主要爬取智联招聘python的职位信息。因为我个人感觉智联招聘的信息更全面一点，不过找工作的网站都大同小异，爬取适合自己的就可以。 完整代码见github: [https://github.com/alpha87/Zhilian](https://github.com/alpha87/Zhilian)

## 代码部分

还是老规矩，先把完整的代码贴上来，然后慢慢分析每个函数的用法。

```python
import time
import random
import requests
import pymongo
from pyquery import PyQuery as pq
from multiprocessing import Pool


def get_page(url):
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4"
    }
    response = requests.get(url, headers=headers)
    if response.status_code == 200:
        return response.text
    return None


def parse_index_page(html):
    doc = pq(html)
    job_urls = doc('#newlist_list_content_table').find('a').items()
    return [url.attr.href for url in job_urls if url.attr.href.startswith('http://jobs')]


def parse_detail_page(html):
    doc = pq(html)
    job = doc('.inner-left.fl h1').text()
    company = doc('.inner-left.fl h2').text()
    company_url = doc('.inner-left.fl h2 a').attr.href
    treatment = doc('.welfare-tab-box span').text()
    salary = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(1) strong').text().strip()
    date = doc('.terminal-ul.clearfix li strong #span4freshdate').text().strip()
    number = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(7) strong').text().strip()
    location = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(2) strong a').text().strip()
    education = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(6) strong').text().strip()
    catrgory = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(8) strong').text().strip()
    experience = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(5) strong').text().strip()
    property = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(4) strong').text().strip()
    address = doc('.tab-cont-box .tab-inner-cont h2').text().strip()
    demand = doc('.tab-inner-cont p').text()
    yield {
        "工作": job,
        "公司": company,
        "公司介绍": company_url,
        "待遇": treatment,
        "职位月薪": salary,
        "发布日期": date,
        "招聘人数": number,
        "工作性质": property,
        "地点": location,
        "工作经验": experience,
        "最低学历": education,
        "职位类别": catrgory,
        "职位描述": demand,
        "工作地点": address
    }


def save_to_mongo(content):
    client = pymongo.MongoClient()
    database = client['Job']
    collection = database['Zhilian']
    if collection.update({'职位描述': content['职位描述']}, {'$set': content}, True) and content['工作'] != "":
        print("Saved", content["工作"], content["职位月薪"])
    else:
        print("***************\nFailed! {}\n***************".format(content["公司"]))


def save_catrgory(content):
    with open("catrgory.txt", "a") as f:
        f.write(content)
        print("职位类别已录入...")


def save_education(content):
    with open("education.txt", 'a') as f:
        f.write(content)
        print("最低学历已录入...")


def save_experience(content):
    with open("experience.txt", 'a') as f:
        f.write(content)
        print("工作经验已录入...")


def save_demand(content):
    with open("demand.txt", 'a') as f:
        f.write(content)
        print("职位描述已录入...")


def save_location(content):
    with open("location.txt", "a") as f:
        f.write(content)
        print("地点已录入...")


def save_salary(content):
    with open("salary.txt", "a") as f:
        f.write(content)
        print("职位月薪已录入...")


def save_treatment(content):
    with open("treatment.txt", "a") as f:
        f.write(content)
        print("待遇已录入...")


def main(page):
    url = "http://sou.zhaopin.com/jobs/searchresult.ashx?jl=%E5%85%A8%E5%9B%BD&kw=python&sm=0&p={}".format(page)
    html = get_page(url)
    docs = parse_index_page(html)
    print("休息后继续...")
    time.sleep(random.randint(1,5))
    for doc in docs:
        html = get_page(doc)
        job_infors = parse_detail_page(html)
        for job_infor in job_infors:
            save_to_mongo(job_infor)
            save_demand(job_infor['职位描述'])
            save_location(job_infor['地点'])
            save_salary(job_infor['职位月薪'])
            save_treatment(job_infor['待遇'])
            save_education(job_infor['最低学历'])
            save_catrgory(job_infor['职位类别'])
            save_experience(job_infor['工作经验'])
            print("休息...")
            time.sleep(random.randint(1,5))


if __name__ == '__main__':
    pool = Pool()
    pool.map(main, [page for page in range(1,200)])
    print("\n采集完成!")
```

## 分部讲解

### 库

```python
import pymongo
import requests
from pyquery import PyQuery as pq
from multiprocessing import Pool
```

这些算是爬取信息最常用的库了。

1.  pymongo是python和mongo数据库相关操作的库；
2.  requests这个库应该不用介绍了吧，网站爬虫的老朋友了；
3.  pyquery主要是作为CSS选择器，指定网页中的对应信息；
4.  multiprocessing是多进程库。

### 解析网页

```python
def get_page(url):
    headers = {
        "User-Agent":"Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4"
    }
    response = requests.get(url, headers=headers)
    if response.status_code == 200:
        return response.text
    return None
```

这部分的作用是模拟浏览器访问网页，如果状态码是200就返回网页内容，如果是其他就返回None。因为网页正常响应返回的状态码是200,我们在这里做一个简单的判断。这样的话在之后爬取过程中出现特殊情况我们可以做其他对策。 在这里给大家推荐一个python库：`fake-useragent`。这个库可以模拟多种浏览器，安装也非常简单。 `pip install fake-useragent` 使用也很简单：

```python
from fake_useragent import UserAgent


UA = UserAgent()
UA.Safari
# User-Agent":"Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.3 Safari/533.19.4
```

输入不同的浏览器名就生成对应的请求头。

### 解析工作详细信息网页

```python
def parse_index_page(html):
    doc = pq(html)
    job_urls = doc('#newlist_list_content_table').find('a').items()
    return [url.attr.href for url in job_urls if url.attr.href.startswith('http://jobs')]
```

这部分把上一个函数返回的网页信息用pyquery解析成可以用CSS定位的内容。然后我们获取每一条工作信息的url，因为返回的url有些不符合条件，所以就加了一条判断，http://jobs开头的才是我们需要的职位信息的url。

### 定位工作的详细信息

我们需要获取职位的名称，提供职位的公司名称，公司介绍url，职位月薪，工作地点，发布日期，工作性质，工作经验，最低学历，招聘人数，职位类别，职位描述和工作地址这些信息。

```python
def parse_detail_page(html):
    doc = pq(html)
    job = doc('.inner-left.fl h1').text().strip()
    company = doc('.inner-left.fl h2').text().strip()
    company_url = doc('.inner-left.fl h2 a').attr.href
    treatment = doc('.welfare-tab-box span').text().strip()
    salary = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(1) strong').text().strip()
    date = doc('.terminal-ul.clearfix li strong #span4freshdate').text().strip()
    number = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(7) strong').text().strip()
    location = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(2) strong a').text().strip()
    education = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(6) strong').text().strip()
    catrgory = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(8) strong').text().strip()
    experience = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(5) strong').text().strip()
    property = doc('.terminalpage-left .terminal-ul.clearfix li:nth-child(4) strong').text().strip()
    address = doc('.tab-cont-box .tab-inner-cont h2').text().strip()
    demand = doc('.tab-inner-cont p').text()
    yield {
        "工作": job,
        "公司": company,
        "公司介绍": company_url,
        "待遇": treatment,
        "职位月薪": salary,
        "发布日期": date,
        "招聘人数": number,
        "工作性质": property,
        "地点": location,
        "工作经验": experience,
        "最低学历": education,
        "职位类别": catrgory,
        "职位描述": demand,
        "工作地点": address
    }
```

这些代码分别抓取到上述有关职位的信息。利用生成器生成这些信息。在使用CSS选择器定位的时候如果出现信息不准确的问题，就定位该标签的父标签。一般都能解决，如果还是显示无关的内容，就试试使用正则或者字符串方法处理。

### 保存到mongo数据库

```python
def save_to_mongo(content):
    client = pymongo.MongoClient()
    database = client['Job']
    collection = database['Zhilian']
    if collection.update({'职位描述': content['职位描述']}, {'$set': content}, True) and content['工作'] != "":
        print("Saved", content["工作"], content["职位月薪"])
    else:
        print("***************\nFailed! {}\n***************".format(content["公司"]))
```

这部分用来把爬取到的信息保存到数据库。为了爬取到的内容不重复，就使用数据库的更新方法，以“职位描述”为判断依据，因为职位名称可以有重复，职位描述重复的几率估计是没有。如果成功插入信息就输出Saved，如果插入失败就输出Failed。

### 保存到txt

```python
def save_demand(content):
    with open("demand.txt", 'a') as f:
        f.write(content)
        print("职位描述已录入...")

def save_location(content):
    with open("location.txt", "a") as f:
        f.write(content)
        print("地点已录入...")

def save_salary(content):
    with open("salary.txt", "a") as f:
        f.write(content)
        print("职位月薪已录入...")

def save_treatment(content):
    with open("treatment.txt", "a") as f:
        f.write(content)
        print("待遇已录入...")
```

这些代码把不同的信息分别保存到txt文本中，为了下面的职业分析做准备。

### 运行

```python
def main(page):
    url = "http://sou.zhaopin.com/jobs/searchresult.ashx?jl=%E5%85%A8%E5%9B%BD&kw=python&sm=0&p={}".format(page)
    html = get_page(url)
    docs = parse_index_page(html)
    for doc in docs:
        html = get_page(doc)
        job_infors = parse_detail_page(html)
        for job_infor in job_infors:
            save_to_mongo(job_infor)
            save_demand(job_infor['职位描述'])
            save_location(job_infor['地点'])
            save_salary(job_infor['职位月薪'])
            save_treatment(job_infor['待遇'])

if __name__ == '__main__':
    pool = Pool()
    try:
        pool.map(main, [page for page in range(1,20)])
    except:
        print("可能已经采集完毕。")
```

这部分代码就是将上面的函数运行起来，利用多进程加快爬取速度。 完成了代码的介绍，接下来就是分析我们爬取到的数据了。

## 数据分析

### 职位要求

每份工作都会对应一份公司自己拟定的职位要求，我们在爬取过程中单独保存成txt文件。利用词频统计的方式生成词云。 ![](https://ws4.sinaimg.cn/large/006tKfTcly1g0gj4g8c6xj30hs0hs760.jpg) 这是处理后的职位要求生成的词云。可以看出公司希望求职者具备扎实的专业技能，良好的沟通能力和合作能力。可以是实习生，可以是学徒，当然更好可以是专家，这些在职位要求中都有体现。

### 工作地点

python在一线城市非常受欢迎，北京、上海、深圳就占据了一半。 ![](https://ws2.sinaimg.cn/large/006tKfTcly1g0gj4z8r3cj30gq0a2dg1.jpg) 北上广深这些地方的同学找工作找实习会方便不少啊，需求量还是蛮大的。

### 职业薪资

![](https://ws1.sinaimg.cn/large/006tKfTcly1g0gj5jna31j30hs0arq34.jpg) 这个图大家感受一下，python工作的薪资主要集中在8000~15000元左右，配合上图工作地点的分布，说明北上广地区的工资集中在8000～20000元左右，还是比较有诱惑力的。这也为你继续深入学习python提供了动力啊！

### 工作经验

![](https://ws3.sinaimg.cn/large/006tKfTcly1g0gj6rvc36j30gq0a2aab.jpg) 公司对工作经验的要求还是蛮高的，3～5年出现的次数比较多。

### 员工福利

![](https://ws3.sinaimg.cn/large/006tKfTcly1g0gj6yifz2j30hs0fgwg7.jpg) 给你们一张图感受一下，其实福利还是蛮不错的，还有的总会有的。

### python热度

![](https://ws4.sinaimg.cn/large/006tKfTcly1g0gj76iwroj30hs0eb3z4.jpg) 这张图相信大家已经不是第一次见到了。python已经进了最流行编程语言的前五，而且python还被运用于各个方面，不论是科学计算，数据分析，还是艺术，游戏，都有python的身影。这么有用的语言，有什么理由不坚持下来学习呢？

## 总结

我们爬取了python职位的相关信息，然后利用这些信息做了简单的分析。得出python还是比较有前途的，不论是为了工作，还是自己的兴趣，我们都应该尝试学学这门语言。入门简单，而且网上的资源也很丰富。 文章的最后附上那句经典： **“ Life is short, you need Python. “**