---
title: 初学React（三）
tags:
  - JavaScript
  - React
  - Web
id: '24'
categories:
  - - 前端
date: 2019-02-25 09:12:49
---

![react](https://reactjs.org/logo-og.png) 这一部分主要讲表单，以及相关的受控组件和非受控组件。
<!-- more -->
根据文档给出的例子，我们需要实现该功能：在输入名字后，弹出对话框。

## 使用受控组件实现表单基本操作

先构造出基本部分：

```javascript
import React, {Component} from "react";


export default class GetYourName extends Component {

    constructor (props) {
        super(props);
        this.state = {
            value: ""
        };
    }

    render () {
        return (
            <form>
                <label>姓名：</label>
                <input type="text" value={this.state.value}/>
                <button type="submit">提交</button>
            </form>
        )
    }

}
```

我们会发现这段代码生成的表单，输入框并不能根据我们的输入发生改变，因为输入框的`value`是受`this.state.value`控制的。其值由React控制的输入表单元素称为“**受控组件**”。我们想要改变`value`还和之前的操作一样，需要使用`this.setState()`方法。 我们需要在`input`标签绑定`onChange`方法，在监听输入框改变的同时改变`this.state.vlaue`的值。如果在提交时弹出输入的字符，我们需要为`form`表单绑定`onSubmit`方法，在表单提交的时候完成相应的操作。

```javascript
import React, { Component } from "react";


export default class GetYourName extends Component {

    constructor(props) {
        super(props);
        this.state = {
            value: ""
        };
    }

    render() {
        return (
            <form onSubmit={(event) => {alert("你好，" + this.state.value); event.preventDefault()}}>
                <label>姓名：</label>
                <input
                    type="text"
                    value={this.state.value}
                    onChange={(event) => this.setState({ value: event.target.value })}
                />
                <button type="submit">提交</button>
            </form>
        )
    }

}
```

我们使用受控组件的方式实现了表单的基本交互： ![](https://ws4.sinaimg.cn/large/006tKfTcly1g0juw3f72uj31i40dqtaw.jpg) 表单操作有多种，大同小异，具体操作可以查看官方文档[表单部分](https://www.reactjscn.com/docs/forms.html)。

## 多个输入如何解决

在网页中，难免会遇到多个输入框的操作，我们需要用到输入框的`name`属性。可以通过给每个元素添加一个name属性，来让处理函数根据 event.target.name的值来选择做什么。 当我在看文档时，看到这个标题，我的处理办法是这样的：

```javascript
import React, { Component } from "react";


export default class GetYourName extends Component {

    constructor(props) {
        super(props);
        this.state = {
            guestName: "",
            gender: "male"
        };
    }

    render() {
        return (
            <form onSubmit={(event) => { alert(`你好，${this.state.guestName}！\n性别：${this.state.gender}`);}}>
                <label>姓名：</label>
                <input
                    type="text"
                    value={this.state.value}
                    onChange={(event) => this.setState({ guestName: event.target.value })}
                />
                <br/>
                <label>性别</label>
                <select value={this.state.gender} onChange={(event) => this.setState({ gender: event.target.value })}>
                    <option value="male">男</option>
                    <option value="female">女</option>
                </select>
                <button type="submit">提交</button>
            </form>
        )
    }
}
```

发现和文档的解决办法是有区别的，我在写这部分的时候，每个需要改变的值分别写了`setState`方法。官方文档是写了一个公共的方法，用来处理需要改变的值。 同样实现了功能，但是在有更多表单的时候，我的方法明显不合适，而且在后期维护起来，总不能一个表单一个表单的修改吧。所以学习一下官方的处理方式。

```javascript
import React, { Component } from "react";


export default class GetYourName extends Component {

    constructor(props) {
        super(props);
        this.state = {
            guestName: "",
            gender: "male"
        };

        this.handleInputChange = this.handleInputChange.bind(this);
    }

    handleInputChange(event) {

        const target = event.target;

        this.setState({
            [target.name]: target.value
        })

    }

    render() {
        return (
            <form onSubmit={() => { alert(`你好，${this.state.guestName}！\n性别：${this.state.gender}`); }}>
                <label>姓名：</label>
                <input
                    name="guestName"
                    type="text"
                    value={this.state.value}
                    onChange={this.handleInputChange}
                />
                <br />
                <label>性别</label>
                <select 
                    name="gender"
                    type="select"
                    value={this.state.gender}
                    onChange={this.handleInputChange}
                >
                    <option value="male">男</option>
                    <option value="female">女</option>
                </select>
                <button type="submit">提交</button>
            </form>
        )
    }
}
```

其实我这个例子非常不典型，因为输入框和选择框取值方法是一样的，体现不出来特殊性，可以直接看官方的例子。

```javascript
// 官方例子
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

根据文档，这一部分属于ES6的新用法：

```javascript
handleInputChange(event) {

    const target = event.target;

    this.setState({
        [target.name]: target.value
    })

}
```

很好理解，简单举例：

```javascript
const name="Name"
const gender="Gender"
const info = {
    [name]: "LiLei",
    [gender]: "male"
}
```

其实等于：

```javascript
const info = {
    Name: "LiLei",
    Gender: "male"
}
```

我认为这样写更具有灵活性。

## 非受控组件

> 在大多数情况下，我们推荐使用 受控组件 来实现表单。 在受控组件中，表单数据由 React 组件处理。如果让表单数据由 DOM 处理时，替代方案为使用非受控组件。 要编写一个非受控组件，而非为每个状态更新编写事件处理程序，你可以 使用 ref 从 DOM 获取表单值。

```javascript
import React, { Component } from "react";


export default class GetYourName extends Component {

    render() {
        return (
            <form onSubmit={() => { alert(`你好，${this.target.value}！`); }}>
                <label>姓名：</label>
                <input
                    type="text"
                    ref={(name) => this.target = name}
                />
                <button type="submit">提交</button>
            </form>
        )
    }
}
```

这部分[官方文档](https://www.reactjscn.com/docs/uncontrolled-components.html)写的很清楚，比较好理解。