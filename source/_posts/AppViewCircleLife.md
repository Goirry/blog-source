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

## UIViewController的生命周期

1. loadView
此方法用在`初始化关键view`, 当访问ViewController的view属性时，view如果此时是nil，那么ViewController会自动调用loadView方法来初始化一个UIView并赋值给`self.view`属性。如果没有先调用了`[supper loadView];`，也没有对view初始化，不能调用view的`getter`方法，否则将导致死循环。
如果没有重载loadView方法，则UIViewController会从`Nib`或`StoryBoard`中查找默认的loadView，默认的loadView会返回一个`空白的UIView对象`。

2. viewDidLoad
当ViewController的view对象载入内存后调用，用于对view进行额外的初始化操作。

3. viewWillAppear
在view即将添加到视图层级中(显示给用户看)且任意显示动画切换之前调用(这个时候supperView还是nil)。这个方法中完成任何与视图显示相关的任务，例如改变视图方向、状态栏方向、视图显示样式等

4. viewDidAppear
在view被添加到视图层级中，显示动画切换之后调用(这时view已经添加到supperView中)。在这个方法中执行视图显示相关附件任务，如果重载了这个方法，必须在方法中调用`[supper viewDidAppear];`。

5. viewWillLayoutSubviews
view即将布局其子视图，会在view调用`layoutSubviews`之前调用。比如view的`bounds`属性改变了(例如状态栏从不显示到显示，视图方向变化)，要调整Subviews的位置，在调整之前要做的一些工作就可以在该方法中实现。

6. viewDidLayoutSubviews
view已经布局其子视图，会在view调用完`layoutSubviews`后调用。

7. viewWillDisappear
view即将从superView中移除且移除动画切换之前，此时还没有调用`removeFromSuperview`。

8. viewDidDisappear
view从superView中移除，移除动画切换之后调用，此时已调用`removeFromSuperview`。

9. viewWillUnload
在view对象从内存中释放之前调用。在`iOS6.0`开始就废弃了，该方法不再会调用。

10. viewDidUnload
在view对象从内存中释放之后调用，此时view为`nil`。在`iOS6.0`开始就废弃了，该方法不再会调用。

<!--more-->

## 生命周期方法的调用顺序
清楚生命周期就清楚了应用的整体流程，对代码的执行顺序有所了解，知道系统会在什么情况下调用什么方法，也就可以对程序更加进一步的理解和掌控。闲话不多说，下面我们来看一下应用从启动到终止都调用了哪些方法:

+ 启动应用
```
application: didFinishLaunchingWithOptions:
RootViewController loadView
RootViewController viewDidLoad:
RootViewController viewWillAppear:
RootViewController viewWillLayoutSubviews
RottViewController viewDidLayoutSubviews
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

## 生命周期的流程图片

当应用从开始加载到进入后台:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/app_enter_background.png "应用从开始加载到进入后台的生命周期流程图")

当用户切换应用时:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/app_enter_foreground.png "用户切换应用时的生命周期流程图")

ViewController的生命周期:
![](http://7xnejb.com1.z0.glb.clouddn.com/images/UIViewController_Life_Circle.jpg "ViewController的生命周期图")

以上是生命周期的图片，非常清晰且详细的描述了应用以及ViewController的生命周期

把流程图看懂`iOS应用开发的生命周期`自然了然于胸。
