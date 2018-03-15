---
layout: post
title: matplotlib中pyplot.figure和matplotlib.figure的区别
author: monkcage
category: python
tags: [python,matplotlib,figure]
---

> 在使用matplotlib时，经常看到这样的现象：大部分的都用matplotlib.pyplot.figure来举例子。但都是单独使用matplotlib的情况！
> 然而，在应用的场景下，都需要把matplotlib嵌入到图像设备中，诸如PyQt,WxPython等，而在嵌入设备的场景下，都使用的是matplotlib.figure来举例的！

* 所以在初接触时就非常的困惑了,百度不到结果，Google的结果也是非常少的！所以到目前为止，笔者也还是有疑问的。
  - matplotlib.pyplot是面向过程的，而matplotlib.figure是OO（Object Oriented）。
  - matplotlib.pyplot.figure和matplotlib.figure.Figure类型不同，查看源码可知前者类型为figManager.canvas.figure，后者为Figure(继承于Artist)
  
* 在何种情况下，matplotlib可以嵌入到其他设备(已PyQt为例)
  - 必须是QObject或其子类
> 在嵌入时都会from matplotlib.backends.backend_qtagg import FigureCanvasQTAgg as FigureCanvas

> 在源码中可以看到这样的继承关系 FigureCanvasQTAgg->FigureCanvasQT->QtGui.QWidget,所以可以简单的认为FigureCanvasQTAgg是QWidget类型的！

* 所以出现这种混淆的情况主要是因为一个思维的转变！从面相过程到面相对象。
