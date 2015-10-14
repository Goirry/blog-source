title: "应用及视图的生命周期"
date: 2015-09-15 18:12:36
categories: iOS
tags: [iOS, OC, 生命周期]
---

应用和视图的生命周期，应该算是移动端开发最基本的平台机制了吧。

## 调用顺序
清楚生命周期就清楚了应用的整体流程，对代码的执行顺序有所了解，知道系统会在什么情况下调用什么方法，也就可以对程序更加进一步的理解和掌控。闲话不多说，下面我们来看一下应用从启动到终止都调用了哪些方法:

+ 启动应用
```
application: didFinishLaunchingWithOptions:
RootViewController viewDidLoad:
RootViewController viewWillAppear:
RootViewController viewDidAppear:
applicationDidBecomeActive:
```
+ 从RootViewController通过`Push方式`跳转到SecondViewController
```
SecondViewController viewDidLoad:
RootViewController viewWillDisappear:
SecondViewController viewWillAppear:
RootViewController viewDidDisappear:
SecondViewController viewDidAppear:
// 通过Present方式跳转的话，4、5调换顺序。
```
+ 从SecondViewController通过`Pop方式`跳转到RootViewController
```
SecondViewController viewWillDisappear:
RootViewController viewWillAppear:
SecondViewController viewDidDisappear:
RootViewController viewDidAppear:
SecondViewController dealloc
// 通过Present方式跳转的话，3、4调换顺序
```
+ 按下Home键使App进入后台(`ViewController不会做响应`)
```
applicationWillResignActive:
applicationDidEnterBackground:
```
+ 点击App从后台进入到前台(`ViewController不会做响应`)
```
applicationWillEnterForeground:
applicationDidBecomeActive:
```
+ 下拉系统的通知栏或上拉出系统的底部控制中心(`ViewController不会做响应`)
```
applicationWillResignActive: // Show
applicationDidBecomeActive: // Hide
```

以上就是程序在各种状态下自动调用方法的顺序，这是本人写的Demo打印的顺序，两个文件同时执行方法的顺序可能会出现误差，但是单个文件当中的方法执行顺序是一定的。

不过看到两个`ViewController`跳转时，执行顺序居然会是交叉调用的，也是很有意思。

<!--more-->



