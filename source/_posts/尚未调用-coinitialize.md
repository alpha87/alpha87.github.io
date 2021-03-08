---
title: 尚未调用 CoInitialize
tags:
  - Pyqt5
  - Python
id: '47'
categories:
  - - 编程学习
date: 2019-04-24 10:30:33
---

![](https://cdn.pixabay.com/photo/2017/04/20/13/02/technology-2245662__480.jpg) 用pyqt写UI的时候，同时使用多线程和win32库操作可能会触发以下错误，顺手记录下来。
<!-- more -->
```bash
IDispatch = pythoncom.CoCreateInstance(IDispatch, None, clsctx, pythoncom.IID_IDispatch)
pywintypes.com_error: (-2147221008, '尚未调用 CoInitialize。', None, None)
```

解决办法是导入`pythoncom`库： `import pythoncom` 并在使用线程的地方加入一行： `pythoncom.CoInitialize()` 问题就解决了。