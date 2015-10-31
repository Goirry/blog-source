title: "应用及视图的生命周期"
date: 2015-09-15 18:12:36
categories: iOS
tags: [iOS, OC, 生命周期]
---

应用和视图的生命周期，应该算是iOS开发最基本的平台机制了吧。

## 应用生命周期的代理方法

UIApplicationDelegate协议的代理方法:

应用启动但还没进入状态保存时调用
```
- (BOOL)application:(UIApplication *)application willFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```

应用启动基本完成程序准备开始运行时调用
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```

当应用程序进入活动状态调用
```
- (void)applicationDidBecomeActive:(UIApplication *)application
```

当应用程序将要进入非活动状态调用
```
- (void)applicationWillResignActive:(UIApplication *)application
```

当程序退到后台的时候调用
```
- (void)applicationDidEnterBackground:(UIApplication *)application
```

当程序进入前台的时候调用
```
- (void)applicationWillEnterForeground:(UIApplication *)application
```

当程序将要退出时被调用
```
- (void)applicationWillTerminate:(UIApplication *)application
```

## 生命周期方法的调用顺序
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

## 生命周期的流程图片

当应用从开始加载到进入后台:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/app_enter_background.png "应用从开始加载到进入后台的生命周期流程图")

当用户切换应用时:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/app_enter_foreground.png "用户切换应用时的生命周期流程图")

ViewController的生命周期:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/UIViewController_Life_Circle.jpg "ViewController的生命周期图")

以上是生命周期的图片，非常清晰且详细的描述了应用以及ViewController的生命周期

把流程图看懂`iOS应用开发的生命周期`自然了然于胸。
