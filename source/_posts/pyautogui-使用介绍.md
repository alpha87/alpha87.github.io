---
title: PyAutoGUI 使用介绍
tags:
  - Python
  - 脚本
id: '370'
categories:
  - - 编程学习
date: 2020-06-27 18:51:49
---

这个 python 库是我用的比较多的第三方库了，因为它能帮我自动化操作许多事情，而且跨平台，非常好用。可以说是 python 中的按键精灵了。它不仅可以操作鼠标，还能操作键盘，屏幕截图，还能生成一些简单的交互窗口，在自动化操作方面提供的功能还是非常全面的。
<!-- more -->
# 安装

```bash
python3 -m pip install pyautogui -i https://mirrors.aliyun.com/pypi/simple
```

以下操作都是已经引入了 pyautogui。

```python
import pyautogui
```

# 鼠标操作

```python
# 获取屏幕分辨率
pyautogui.size()

# 获取当前鼠标所在位置
pyautogui.position()
```

这两个方法分别是确定鼠标的移动范围，确定当前鼠标的移动位置。

```python
# 移动到 100, 200 的位置
pyautogui.moveTo(100, 200)

# 基于以上位置 Y 轴移动 50
pyautogui.move(0, 50)

# 在 2 秒内按住鼠标左键拖动到 300, 400 的位置
pyautogui.dragTo(300, 400, 2, button='left')

# 在 2 秒内按住鼠标右键，基于以上位置 X 轴拖动 30
pyautogui.drag(30, 0, 2, button='right')
```

上述操作比较好理解，移动或者拖动鼠标。

```python
# 开始慢，结束快
pyautogui.moveTo(100, 100, 2, pyautogui.easeInQuad)

# 开始快，结束慢
pyautogui.moveTo(100, 100, 2, pyautogui.easeOutQuad)

# 开始和结束慢，中间快
pyautogui.moveTo(100, 100, 2, pyautogui.easeInOutQuad)

# 弹跳移动到 100, 100 （移动范围较小）
pyautogui.moveTo(100, 100, 2, pyautogui.easeInBounce)

# 弹跳移动到 100, 100 （范围较大）
pyautogui.moveTo(100, 100, 2, pyautogui.easeInElastic)
```

这部分操作在一些有人机检测的系统中一定会用到。比如说我们经常遇到网页的验证码拖动图片补全完整图片，如果匀速拖动肯定是不行的，因为正常人的操作是不会匀速拖动鼠标；还有一种情况是在模拟操作游戏，如果鼠标移动过于死板，每次的动作幅度，路径，点击的坐标都相同，也会被检测出使用了辅助软件。所以在使用鼠标操作游戏的时候，一定要加入随机量，比如鼠标移动的路径要随机，我们可以把上述参数放在列表中，每次随机挑选移动方式，点击的坐标随机加或减几个像素，每次点击的停顿时间也随机等等。一般这么操作就不会被查出来了。当然还需要根据玩家的平时在线时间来使用，不能说平时都是下午在线，然后突然每天黑夜刷很多小时。这样的操作一般也会被警告（以我多年阴阳师玩家经验）。扯远了，回正题。

```python
# 点击
pyautogui.click()

# 移动到 100, 200 点击
pyautogui.click(x=100, y=200)

# 使用右键点击，可选 left, middle, right
pyautogui.click(button='right')

# 双击
pyautogui.click(clicks=2)

# 双击，单击间隔时间 0.25 秒
pyautogui.click(clicks=2, interval=0.25)

# 鼠标按下
pyautogui.mouseDown()

# 鼠标抬起
pyautogui.mouseUp()
```

# 键盘操作

```python
# 输入，每个字符间隔 0.25 秒
pyautogui.write('Hello world!', interval=0.25)

# 按住 shift
pyautogui.keyDown('shift')

# 按下 left 键
pyautogui.press('left')

# 弹起 shift
pyautogui.keyUp('shift')
```

上述操作实现按住 shift 同时按下 left 键。 如果需要按下多个键，可以使用：

```python
pyautogui.press(['left', 'left', 'left'])
```

如果键相同可以：

```python
pyautogui.press('left', presses=3)
```

pyautogui 支持的按键：

```bash
['\t','\n','\r',' ','!','"','#','$','%','&',"'",'(',')','*','+',',','-','.','/',
'0','1','2','3','4','5','6','7','8','9',':',';','<','=','>','?','@','[','\\', ']', 
'^', '_', '`', 'a', 'b', 'c', 'd', 'e','f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 
'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '{', '|', '}', '~', 
'accept', 'add', 'alt', 'altleft', 'altright', 'apps', 'backspace', 'browserback', 
'browserfavorites', 'browserforward', 'browserhome', 'browserrefresh', 'browsersearch', 
'browserstop', 'capslock', 'clear', 'convert', 'ctrl', 'ctrlleft', 'ctrlright', 
'decimal', 'del', 'delete', 'divide', 'down', 'end', 'enter', 'esc', 'escape', 
'execute', 'f1', 'f10', 'f11', 'f12', 'f13', 'f14', 'f15', 'f16', 'f17', 'f18', 
'f19', 'f2', 'f20', 'f21', 'f22', 'f23', 'f24', 'f3', 'f4', 'f5', 'f6', 'f7', 
'f8', 'f9', 'final', 'fn', 'hanguel', 'hangul', 'hanja', 'help', 'home', 'insert',
 'junja', 'kana', 'kanji', 'launchapp1', 'launchapp2', 'launchmail', 'launchmediaselect', 
 'left', 'modechange', 'multiply', 'nexttrack', 'nonconvert', 'num0', 'num1', 'num2', 
 'num3', 'num4', 'num5', 'num6', 'num7', 'num8', 'num9', 'numlock', 'pagedown', 'pageup', 
 'pause', 'pgdn', 'pgup', 'playpause', 'prevtrack', 'print', 'printscreen', 'prntscrn', 
 'prtsc', 'prtscr', 'return', 'right', 'scrolllock', 'select', 'separator', 'shift', 
 'shiftleft', 'shiftright', 'sleep', 'space', 'stop', 'subtract', 'tab','up', 
 'volumedown', 'volumemute', 'volumeup', 'win', 'winleft', 'winright', 
 'yen', 'command', 'option', 'optionleft', 'optionright']
```

# 消息弹窗

## alert()

```python
pyautogui.alert(text='⚠️这是一段警告', title='alert 测试', button='OK')
```

会弹出如下提示框： ![alert](https://i.loli.net/2020/06/27/KCYxOpRmLJezEF7.png)

## confirm()

```python
a = pyautogui.confirm(text='对话框测试', title='标题', buttons=['OK', 'Cancel'])
print(a)
```

我这么写你也应该明白了，`confirm()` 方法会传递参数。这样我们在操作过程中可以实现简单选择。 ![confirm](https://i.loli.net/2020/06/27/iGEFep3hoJU6KuX.png)

## prompt()

```python
a = pyautogui.prompt(text='请输入学号', title='标题' , default='学号')
print(a)
```

选择 OK 获取用户输入，选择 Cancel 返回 None。 ![](https://i.loli.net/2020/06/27/E7TuvUa3tACw9Mz.png)

## password()

```python
a = pyautogui.password(text='输入密码', title='标题', default='', mask='*')
print(a)
```

选择 OK 获取用户输入，选择 Cancel 返回 None。只是输入的字符串都会被 `mask` 代替。 ![password](https://i.loli.net/2020/06/27/AbIr68B2MDxP9pa.png)

# 屏幕截图

```python
pyautogui.screenshot('my_screenshot.png')
```

屏幕截图比较有用，根据截图找图感觉精度不高，所以就不推荐了。 以上就是 pyautogui 的使用介绍了，基本包含了所有常用操作。 如果还觉得不够可以看[官方文档](https://pyautogui.readthedocs.io/en/latest/index.html)和[源码](https://github.com/asweigart/pyautogui)。