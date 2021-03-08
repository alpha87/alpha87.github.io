---
title: 初学React（二）
tags:
  - JavaScript
  - React
  - Web
id: '22'
categories:
  - - 前端
date: 2019-02-18 19:09:54
---

![react](https://reactjs.org/logo-og.png) 学习react就不得不提提[JSX](https://react.docschina.org/docs/introducing-jsx.html)。
<!-- more -->
## 什么是JSX

对于我这个JavaScript和react同时学习的外行来看：

```JavaScript
const element = <h1>Hello, world!</h1>;
```

这行代码并没有什么问题，但是认真一看，这明显就是js和html的结合体嘛。直接运行是会报语法错误的。 这种奇怪的写法官方称其为**JSX**。

> JSX 是一种 JavaScript 的语法扩展。 我们推荐在 React 中使用 JSX 来描述用户界面。JSX 乍看起来可能比较像是模版语言，但事实上它完全是在 JavaScript 内部实现的。

JSX实际上是一种语法糖，是将`React.createElement()`简化的语法糖。 文档中也介绍道：

> Babel 转译器会把 JSX 转换成一个名为 React.createElement() 的方法调用。

```JavaScript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

和

```JavaScript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

效果是一样的。 我认为初学并没必要理解那么深刻，现在可以简单的认为JSX就是一种可以在html标签里，可以使用`{`和`}`来构造表达式的一种语法糖。方便我们专心构造html页面，而非将注意力集中在如何使用`React.createElement`方法上。 简单记忆上段话就是：

```JavaScript
const element = <h2>1 + 1 = { 1 + 1 }</h2>;
```

## 元素渲染

根据文档的学习顺序，接下来需要学习元素渲染，也就开始了构建页面。 写一个和时间相关的demo。 新建文件`DigitalTime.js` 写入如下代码：

```JavaScript
import React, { Component } from 'react'

class DigitalTime extends Component {

    render() {
        return (
            <h1>
                现在是{new Date().toLocaleTimeString()}
            </h1>
        );
    }
}

export default DigitalTime;
```

然后在`index.js`文件中引入使用。启动服务后，我们能看到页面上显示`现在是下午9:19:51`，但是这个页面只是生成了此刻的时间点，页面上的字符并不会随着时间自动改变。

> React 元素都是 **immutable** 不可变的。当元素被创建之后，你是无法改变其内容或属性的。一个元素就好像是动画里的一帧，它代表应用界面在某一时间点的样子。

但是我们需要让时间动起来。 写成函数的形式（官方文档有介绍）：

```JavaScript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );

  // 这里直接渲染
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```

虽然实现了目标，但是这种写法并不好。我们需要将时间改为动态展示，但是上述方法却是以更新`ReactDOM.render()`方法来动态展示时间，这并不是最佳实践。 所以官方提供了第二种写法，[将函数转成类](https://react.docschina.org/docs/state-and-lifecycle.html#%E5%B0%86%E5%87%BD%E6%95%B0%E8%BD%AC%E6%8D%A2%E4%B8%BA%E7%B1%BB)：

```JavaScript
import React, { Component } from 'react'

class DigitalTime extends Component {

    constructor(props) {
        super(props)
        this.state = {
            date: new Date()
        }
    }

    // 挂载
    componentDidMount() {
        this.clock = setInterval(() => this.setState({date: new Date()}), 1000)
    }

    // 卸载
    componentWillUnmount() {
        clearInterval(this.clock)
    }

    render() {
        return (
            <h1>
                现在是{this.state.date.toLocaleTimeString()}
            </h1>
        );
    }
}

export default DigitalTime;
```

内容有点多，这块我也看了半天。这里涉及到了react组件的生命周期。 先说`constructor()`方法。constructor()是类构造方法， 用来初始化`this.state`。感觉类似python类的`__init__()`方法。 `super()`和python中的意义类似，子类在构造`__init__()`方法时要显式继承。 `state`简单来说就是记录状态，在栗子中我们记录date的状态。

## 组件

函数定义的组件和类定义的组件是可以相互改写的。 例如官方提供的例子：

```javascript
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
}
```

可以改写为：

```javascript
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>;
    }
}
```

## 生命周期方法

![react组件的生命周期](https://upload-images.jianshu.io/upload_images/4118241-d979d05af0b7d4db.png) 挂载组件和卸载组件都很重要，当应用程序中有许多组件时，释放资源很重要。 `componentDidMount()`方法的作用是安装组件后立即调用。 我理解的就是渲染后立即执行该方法。该方法里定义了一个`clock`方法，用来更新时间。 更新时间使用`setInterval()`方法，使用方法是：

> function setInterval(callback: (...args: any\[\]) => void, ms: number, ...args: any\[\]): NodeJS.Timeout (+1 overload)

可以传入两个参数，第一个是回调函数，第二个是时间间隔。 使用`this.setState()`方法每一秒钟修改`state`状态里的值。 这样就完成了时间的动态展示。 使用`setInterval()`时用到了箭头函数，虽然知道明白什么意思，但是理解不了为什么不能用`function() {}`来代替。 查了一些资料([JS中的箭头函数与this](https://juejin.im/post/5aa1eb056fb9a028b77a66fd))发现，还是对箭头函数不了解。 有例子讲到在`setInterval()`方法里使用箭头函数，箭头函数中`this`默认使用的是外层的`this`，也就是指定外侧的类。 这样就可以使用`this.setState()`方法更新`state`的属性。 如果实在不想用箭头函数，那就需要`function`绑定`this`。可以这么写：

```JavaScript
componentDidMount() {
        // this.clock = setInterval(() => this.setState({date: new Date()}), 1000)
        this.clock = setInterval(
            function() { this.setState({date: new Date()}) }.bind(this), 1000
        )
    }
```

这样的效果是一样的（当然这个解决办法是问小伙伴解答的）。 当然还可以这样写：

```JavaScript
import React, { Component } from 'react'

class DigitalTime extends Component {

    constructor(props) {
        super(props)
        this.state = {
            date: new Date()
        }

        // 这里绑定
        this.updateTime = this.updateTime.bind(this)
    }

    updateTime() {
        this.setState({date: new Date()})
    }

    // 挂载
    componentDidMount() {
        // this.clock = setInterval(() => this.setState({date: new Date()}), 1000)
        // this.clock = setInterval(
        //     function() { this.setState({date: new Date()}) }.bind(this), 1000
        // )
        this.clock = setInterval(this.updateTime, 1000)
    }

    // 卸载
    componentWillUnmount() {
        clearInterval(this.clock)
    }

    render() {
        return (
            <h1>
                现在是{this.state.date.toLocaleTimeString()}
            </h1>
        );
    }
}

export default DigitalTime;
```

有关函数绑定这块[官方也有例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)：

```JavaScript
var module = {
  x: 42,
  getX: function() {
    return this.x;
  }
}

var unboundGetX = module.getX;
console.log(unboundGetX()); // The function gets invoked at the global scope
// expected output: undefined

var boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
// expected output: 42

```

比较好理解。