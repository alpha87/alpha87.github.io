---
title: 初学React（四）
tags:
  - JavaScript
  - React
  - Web
id: '25'
categories:
  - - 前端
date: 2019-02-28 15:13:44
---

![react](https://reactjs.org/logo-og.png) 我们在构建网页的时候，肯定不只有一个组件。当同时存在多个组件，且多个组件需要共用一些数据时，就产生了**状态提升**的概念，也就是多个组件共用一个状态（数据）。之所以称之为提升，可能就是子组件的数据被提升到了父组件。
<!-- more -->
在看官方文档的时候，发现其绕了一个大圈子。 文档中的demo是先构造一个摄氏温度判断水是否能烧开（一个标准大气压下）。又使用华氏度来判断水的沸点，然后将这两者相结合，在输入摄氏度的时候同步展示华氏度，反之亦然。因为摄氏度和华氏度都需要接收用户输入，此时才用到了状态提升的概念。 不过文档一步一步讲的非常仔细，保证每个人都能看懂。

## 代码部分

```javascript
import React, { Component } from 'react';


const scaleNames = {
    c: '摄氏',
    f: '华氏'
};


class BoilingVerdict extends Component {

    render() {
        const celsius = this.props.celsius;
        if (celsius >= 100) {
            return (<h2>水会烧开</h2>);
        } else {
            return (<h2>水不会烧开</h2>);
        }
    }

}


function toCelsius(fahrenheit) {
    return (fahrenheit - 32) * 5 / 9;
}


function toFahrenheit(celsius) {
    return (celsius * 9 / 5) + 32;
}


function tryConvert(temperature, convert) {
    const input = parseFloat(temperature);

    if (Number.isNaN(input)) {
        return '';
    }

    const output = convert(input);
    const rounded = Math.round(output * 1000) / 1000;

    return rounded.toString();
}


class Temperature extends Component {

    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(e) {
        this.props.onTemperatureChange(e.target.value);
    }

    render() {

        const temperature = this.props.temperaturer;
        const scale = this.props.scale;

        return (
            <div>
                <form>
                    <fieldset>
                        <legend>
                            <h1>输入{scaleNames[scale]}温度</h1>
                        </legend>

                        <b>{scaleNames[scale]}度: </b>

                        <input
                            value={temperature}
                            onChange={this.handleChange}
                        />

                    </fieldset>
                </form>
            </div>
        );
    }
}


export default class Calculator extends Component {

    constructor(props) {
        super(props);
        this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
        this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
        this.state = {
            temperature: '',
            scale: ''
        };
    }

    handleCelsiusChange(temperaturer) {
        this.setState({ scale: 'c', temperature: temperaturer });
    }

    handleFahrenheitChange(temperaturer) {
        this.setState({ scale: 'f', temperature: temperaturer });
    }

    render() {

        const scale = this.state.scale;
        const temperature = this.state.temperature;

        const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
        const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

        return (
            <div>
                <Temperature
                    scale="c"
                    temperaturer={celsius}
                    onTemperatureChange={this.handleCelsiusChange} />

                <Temperature
                    scale="f"
                    temperaturer={fahrenheit}
                    onTemperatureChange={this.handleFahrenheitChange} />

                <BoilingVerdict celsius={parseFloat(celsius)} />
            </div>
        );
    }
}
```

## 梳理

我们使用`Calculator`组件共享用户的输入（状态提升），`Temperature`组件用来复用输入框。不同输入框的`scale`不同，用来对应不同的名称，输入框不同的表头是根据`scale`由`scaleNames`转换的。其他部分就更好理解了，使用`setState`方法修改`state`的值，摄氏度和华氏度的转换等等。

## React开发者工具

官方还提供了[React 开发者工具](https://github.com/facebook/react-devtools)。 使用效果基本就是这个样子的，可以清晰看出react组件树： ![](https://ws4.sinaimg.cn/large/006tKfTcly1g0ne5xr8byj31c00u0dpp.jpg)