---
title: 使用 Pyqt5 Designer 构建桌面应用
tags:
  - Pyqt5
  - Python
id: '53'
categories:
  - - 编程学习
date: 2019-04-27 08:40:28
---

![](https://cdn.pixabay.com/photo/2014/05/10/19/23/presentation-341444__480.png) 文章分为三部分，主要是**界面的设计**，**py文件的代码编写**和**其余部分的小修小补**。
<!-- more -->
## 设计界面

执行`pip install pyqt5-tools`命令，安装好可以在包目录（类似`C:\Programs\Python\Python37-32\Lib\site-packages\pyqt5_tools`。）下找到`designer.exe`应用程序。 打开后可以看到如下界面： ![应用界面](https://i.loli.net/2019/04/27/5cc40db074a15.png) 应用的左边是构成用户界面的各种组件，右边是窗口以及组件的属性和设置，中间就是你构建用户界面的窗口。 我们可以结合自己的需求，发挥想象力设计出独特的用户界面。 ![Onmyoji](https://i.loli.net/2019/04/27/5cc40f32ba7fc.png) 设计过程中，可以使用`Ctrl+R`预览界面。也可以初步体验一些交互功能。 我们在界面右边靠下部分，注意到有`Signal/Slot Editor`，我们可以在这里绑定一些事件。 例如我将`spinBox`的`valueChanged(int)`和`progressBar`的`setValue(int)`绑定，我们在预览界面的时候，不断增加数值，可以看到进度条也在同时改变。这些交互部分在生成代码之后也有体现。 ![预览](https://i.loli.net/2019/04/27/5cc4147f12122.png)

## 转换为py代码

设计好界面以后，我们需要转换成python代码，和我们的程序相结合，从单纯的操作或命令行输出，到用户界面的诞生，我们还需要基于设计好的界面，再添砖加瓦。 转换为python代码是非常简单的，只需要在设计保存好的ui文件目录下，执行命令： `pyuic5 -o test.py test.ui` 这里需要两个参数，第一个是转换成py代码的文件名，第二个是需要转换成py文件的ui文件。 转换好以后就可以通过代码与设计好的界面相结合了。 直接生成好的文件，我们还需要在底部添加如下代码才能运行：

```python
if __name__ == '__main__':
    import sys
    app = QtWidgets.QApplication(sys.argv)
    MainWindow = QtWidgets.QMainWindow()
    ui = Ui_Form()
    ui.setupUi(MainWindow)
    MainWindow.show()
    sys.exit(app.exec_())
```

现在我们只是需要以代码的形式来生成用户见面，不需要考虑太多。我个人认为使用命令行生成的代码并不美观，因为以上生成的代码执行过程中，需要给`Ui_Form`类的`setupUi`方法传递`QtWidgets.QMainWindow()`，其实更好的写法是继承它而不是作为参数传递它，如果使用继承，将代码里的`Form`直接更换为`self`，更为合理一些。当然现在不着急改这些。

## 组件交互

这里简单罗几个我在使用过程中遇到的，组件需要和方法绑定的交互操作。

### pushButton

`self.pushButton.clicked.connect(self.show_good_key)`

### spinBox

`self.spinBox.valueChanged['int'].connect(self.set_num)`

### lineEdit

`self.lineEdit.textChanged['QString'].connect(self.set_num)` 结合设计界面时的绑定操作，还是很好分析出每个组件具体有怎样的属性，以及如何绑定或改变。注意括号里都是方法名，名称尾部没有括号，因为我们只是将方法和组件绑定在一起，当组件发生改变时再调用方法。

## 补充

### 多线程的支持

我们设计好界面并和程序相结合，当我们执行的时候会发现点击一个按钮后，如果程序正在执行，那么界面会呈现假死状态，是因为现在的程序还只支持单线程工作，就好比在开发web界面的时候，如果设计到后台发送邮件，如果不使用多线程或`celery`队列等方式，那么页面也会呈现假死状态。 官方提供了QtCore.QThread类，但是由于我的程序比较特殊，无法使用这种方法，所以另辟蹊径，使用python自带的`threading`库。 具体实现看以下代码，需要根据实际情况自行修改：

```python
# 导入 Thread
from threading import Thread

def thread_run(self):
    """创建线程"""
    thr = Thread(target=self.app_run)
    thr.start()

def app_run(self):
    """这部分是耗时操作"""
    ...
    self.main()

# 调用创建线程的方法
self.pushButton.clicked.connect(self.thread_run)
```

简单来说很简单，就是将具有耗时操作的方法，单独用线程创建，按钮绑定创建线程的方法即可。 但是在实际过程中我们会发现存在这样一个问题，在你关闭界面后，如果线程所在的任务没有执行完毕，线程还是会继续执行，这一般来说不是我们想要的结果，我们一般希望在关闭界面后，所有任务也一同结束。 所以需要在`thread_run`方法里添加一行：

```python
def thread_run(self):
    """创建线程"""
    thr = Thread(target=self.app_run)
    thr.setDaemon(True)
    thr.start()
```

将子线程设置为守护线程，在主程序退出的时候同时结束任务，无需等待子线程执行完。 这样以后我们在关闭程序界面的时候，按钮绑定的任务也会同时结束。

### 尚未调用 CoInitialize

```bash
IDispatch = pythoncom.CoCreateInstance(IDispatch, None, clsctx, pythoncom.IID_IDispatch)
pywintypes.com_error: (-2147221008, '尚未调用 CoInitialize。', None, None)
```

因为我的程序中同时使用了多线程和win32，所以引发了这个问题。解决办法也很简单。 `import pythoncom` 并在使用线程的地方加入一行： `pythoncom.CoInitialize()` 例如上边的例子就是在`app_run`方法中添加一行

```python
def app_run(self):
    """这部分是耗时操作"""
    pythoncom.CoInitialize()
    self.main()
```

这样就不会报错了。

### 点击按钮弹出新窗口

需要我们再设计一个界面，例如： ![鼓励作者](https://i.loli.net/2019/04/27/5cc41da085b28.png) 设计好后使用命令行转换成py代码。 然后在主界面中导入，并将该方法绑定需要弹出窗口的按钮即可。 例如：

```python
def show_good(self):
    """展示赞赏码"""
    from good import Ui_Form
    ui = Ui_Form()
    MainWindow = QtWidgets.QDialog()
    ui.setupUi(MainWindow)
    MainWindow.show()
    MainWindow.exec_()
```

这样下来一个简单的桌面应用就搞定了。 **具体可以参考[阴阳师桌面版助手](https://github.com/alpha87/OnmyojiHelperGUI/releases)**