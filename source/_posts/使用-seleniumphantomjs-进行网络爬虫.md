---
title: 使用 selenium+PhantomJS 进行网络爬虫
tags:
  - PhantomJS
  - Python
  - selenium
  - 爬虫
id: '63'
categories:
  - - 编程学习
date: 2017-02-26 11:03:54
---

![](https://cdn.pixabay.com/photo/2017/01/18/08/25/social-media-1989152__480.jpg) Selenium本是一个用于Web应用程序测试的工具。Selenium测试直接运行在浏览器中，就像真正的用户在操作一样。PhantomJS是一个基于WebKit的服务器端 JavaScript API。它全面支持web而不需浏览器支持。使用selenium和PhantomJS这两个工具来爬虫，可以实现很多功能。
<!-- more -->
# Selenium和PhantomJS

selenium是一个强大的网络数据采集工具，最初是为了网站自动化测试而开发的。它可以让浏览器自动加载页面，获取需要的数据，也可以截屏，或判断网站上某些动作是否发生。 PhantomJS是一个无头浏览器，它不会向用户展示网页的图形界面。 简单理解selenium就是完成模拟用户所有操作的工具，而PhantomJS则是一个没有图形界面，可供selenium实现各种操作的浏览器。

# 代码部分

```python
from selenium import webdriver

browser = webdriver.PhantomJS(executable_path="C:\phantomjs.exe")
browser.implicitly_wait(30)
browser.get("http://210.31.122.109")

browser.find_element_by_name("txtUserName").clear()
browser.find_element_by_name("txtUserName").send_keys("201303010121")
browser.find_element_by_name("TextBox2").clear()
browser.find_element_by_name("TextBox2").send_keys("password")

browser.save_screenshot(str("登录界面.jpg"))
browser.find_element_by_name("txtSecretCode").send_keys(input("输入验证码\n>>> "))
browser.find_element_by_class_name("btn_dl").click()
print(browser.page_source)
browser.close()
```

## 分析代码

\`\`\`from selenium import webdriver <pre><code class=""><br />用来导入selenium库。 \`\`\`browser = webdriver.PhantomJS(executable\_path="C:\\phantomjs.exe") 用来指明PhantomJS可执行文件的路径。 \`\`\`browser.implicitly\_wait(30) <pre><code class=""><br />是隐式等待30秒。一旦设定，browser对象实例的整个生命周期的隐式调用也就设定好了。 \`\`\`browser.get("http://210.31.122.189") 打开指定网址。

```python
browser.find_element_by_name("txtUserName").clear()
browser.find_element_by_name("txtUserName").send_keys("201303010121")
browser.find_element_by_name("TextBox2").clear()
browser.find_element_by_name("TextBox2").send_keys("password")
```

这四行代码分别对应的是： 1.清除用户名中原来的信息。 2.键入学号。 3.清除密码中原来的信息。 4.键入密码。

```python
browser.save_screenshot(str("登录界面.jpg"))
browser.find_element_by_name("txtSecretCode").send_keys(input("输入验证码\n>>> "))
browser.find_element_by_class_name("btn_dl").click()
print(browser.page_source)
```

这四行代码分别实现的功能是： 1.把登录页面截图，保存到当前文件夹下。（保存登录页面的截图是为了可以查看当前网页的验证码。） 2.键入验证码。 3.点击登录。 4.打印登录以后页面的源码。 \`\`\`browser.close() <pre><code class=""><br />用来关闭操作。 \[!\[用户登录界面源码\](http://olzlqlgy5.bkt.clouddn.com/%E6%95%99%E5%8A%A1%E7%99%BB%E5%BD%95%E7%95%8C%E9%9D%A2%E6%BA%90%E7%A0%81.jpg)\](http://olzlqlgy5.bkt.clouddn.com/%E6%95%99%E5%8A%A1%E7%99%BB%E5%BD%95%E7%95%8C%E9%9D%A2%E6%BA%90%E7%A0%81.jpg) # selenium用法拓展 ## 元素定位 Selenium提供了下面的方法进行元素定位： find\_element\_by\_id find\_element\_by\_name find\_element\_by\_xpath find\_element\_by\_link\_text find\_element\_by\_partial\_link\_text find\_element\_by\_tag\_name find\_element\_by\_class\_name find\_element\_by\_css\_selector 寻找多个元素（下列方法会返回一个列表）： find\_elements\_by\_id find\_elements\_by\_name find\_elements\_by\_xpath find\_elements\_by\_link\_text find\_elements\_by\_partial\_link\_text find\_elements\_by\_tag\_name find\_elements\_by\_class\_name find\_elements\_by\_css\_selector 例如： \`\`\`<html> <body> <form id="loginForm"> <input name="username" type="text" /> <input name="password" type="password" /> <input name="continue" type="submit" value="Login" /> </form> </body> <html> 可以这样定位表单元素form： \`\`\`login\_form = driver.find\_element\_by\_id('loginForm') <pre><code class=""><br />## 异步加载 > 现在很多Web应用都在使用AJAX技术。浏览器载入一个页面时，页面内的元素可能是在不同的时间载入的， 这会加大定位元素的困难程度，因为元素不在DOM里，会抛出 ElementNotVisibleException 异常， 使用 waits，我们就可以解决这个问题。 ### 显式等待 显式的 waits 等待一个确定的条件触发然后才进行更深一步的执行。 最糟糕的的做法是 time.sleep()，这指定的条件是等待一个指定的时间段。 这里提供一些便利的方法让你编写的代码只等待需要的时间，WebDriverWait 结合ExpectedCondition 是一种实现的方法： \`\`\`python from selenium import webdriver from selenium.webdriver.common.by import By from selenium.webdriver.support.ui import WebDriverWait from selenium.webdriver.support import expected\_conditions as EC driver = webdriver.Firefox() driver.get("http://somedomain/url\_that\_delay\_loading") try: element = WebDriverWait(driver,10).until( EC.presence\_of\_element\_located((By.ID,"myDynamicElement")) ) finally: driver.quit() 这段代码会等待10秒，如果10秒内找到元素则立即返回，否则会抛出 TimeoutException 异常， WebDriverWait默认每500毫秒调用一下 ExpectedCondition`直到它返回成功为止。`ExpectedCondition 类型是布尔的，成功的返回值就是true,其他类型的 ExpectedCondition 成功的返回值就是 not null。

#### Expected Conditions

自动化网页操作时，有许多频繁使用到的通用条件。下面列出的是每一个条件的实现。 Selenium + Python 提供了许多方便的方法，因此你不需要自己编写 expected\_condition 的类， 或者创建你自己的通用包。 **title\_is** **title\_contains** **presence\_of\_element\_located** **visibility\_of\_element\_located** **visibility\_of** **presence\_of\_all\_elements\_located** **text\_to\_be\_present\_in\_element** **text\_to\_be\_present\_in\_element\_value** **frame\_to\_be\_available\_and\_switch\_to\_it** **invisibility\_of\_element\_located** **element\_to\_be\_clickable it** **is Displayed and Enabled.** **staleness\_of** **element\_to\_be\_selected** **element\_located\_to\_be\_selected** **element\_selection\_state\_to\_be** **element\_located\_selection\_state\_to\_be** **alert\_is\_present**

```python
from selenium.webdriver.support import expected_conditions as EC
wait = WebDriverWait(driver,10)
element = wait.until(EC.element_to_be_clickable((By.ID,'someid')))
```

expected\_conditions 模块包含了一系列预定义的条件来和WebDriverWait使用。

### 隐式等待

当我们要找一个或者一些不能立即可用的元素的时候，隐式 waits 会告诉WebDriver轮询DOM指定的次数，默认设置是0次。 一旦设定，WebDriver对象实例的整个生命周期的隐式调用也就设定好了。

```python
from selenium import webdriver

driver = webdriver.Firefox()
driver.implicitly_wait(10) 
driver.get("http://somedomain/url_that_delays_loading")
myDynamicElement = driver.find_element_by_id('myDynamicElement')
```

## 其他用法

这部分是我看其他教程总结的一些零碎用法。 1.浏览器最大化：

```
browser.maximize_window()
```

2.查看页面：

```
browser.page_sources
```

3.设置浏览器宽和高：

```
browser.set_window_size(480,800)
```

4.操作对象常用方法：

```
click() #点击对象
send_keys() #在对象上模拟输入
text #获取元素的文本信息
submit() #用于提交表单，某些情况下和click()用法相同
```

5.截图：

```
browser.save_screenshot
```

6.用链接文本定位超链接：

```
#源码部分
<a href="continue.html">Continue</a>
```

```
#定位
continue_link = browser.find_element_by_link_text('Continue')
```

7.使用cookies：

```
cookies = {
  "name":"foo"
  "value":"bar"
}
browser.add_cookie(cookies)
# And now output all the available cookies for the current URL
driver.get_cookies()
```

8.前进和后退页面：

```
browser.forward() #前进
browser.back() #后退
```