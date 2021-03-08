---
title: 初学React（一）
tags:
  - JavaScript
  - React
  - Web
id: '26'
categories:
  - - 前端
date: 2019-02-17 08:18:01
---

![react](https://reactjs.org/logo-og.png) 瞎搞来瞎搞去，感觉前端还是挺有意思的，抽一段时间时间学习学习React，感受一下前端的魅力。也写几篇文章记录学习过程（要不然过几天脑袋一片空白，什么都不记得）。 这是学习React的第一天，主要以搭建环境和写一个小的测试demo为主。demo想都不用想，那必须是`Hello, World!`。
<!-- more -->
## 安装Create React App工具

首先需要安装[Create React App](https://github.com/facebook/create-react-app)，有了这个工具，我们就能轻松创建react项目。 但是这个工具需要安装node，Mac下使用brew安装。 `brew install node` node安装完成后就可以使用npm安装Create React App工具了。 `npm install -g create-react-app`

## 创建项目

到指定目录下，执行 `create-react-app my_project` 耐心等待项目的安装。 项目安装完成后，目录树基本是这个样子的：

```
.
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── Welcome.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js
```

执行 `npm start` 在本地的3000端口成功启动了react项目，看到的是react的欢迎页面。

## 第一个demo

配置好开发环境后，我们需要写一个小demo测试一波。 在src目录下新建一个`Welcome.js`文件，用来写我们的第一段代码。

```JavaScript
import React, {Component} from 'react';

class Welcome extends Component {
  render() {
    return (
      <h1>Hello, world!</h1>
    );
  }
}

export default Welcome;
```

写好后保存。修改`index.js`文件，导入我们新写好的demo： `import Welcome from './Welcome';` 然后将 `ReactDOM.render(<App />, document.getElementById('root'));` 改为 `ReactDOM.render(<Welcome />, document.getElementById('root'));` 这一步操作是将React元素渲染到DOM节点，通过把它们传递给`ReactDOM.render()`的方法来将其渲染到页面上。 回到终端，再次使用`npm start`启动项目，react的欢迎页面就改成我们写好的`Hello, World!`了。