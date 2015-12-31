title: "Objective-C 语法(四) OC内存"
date: 2015-08-09 18:15:36
categories: OC
tags: [OC,Objective-C]
---

## OC内存

每个OC对象都有自己的引用计数器，是整数，表示该对象被引用的次数。
当引用计数器为`0`的时候，对象就会被系统回收。

每个OC对象内部专门有`4个字节`的存储空间来存引用计数器。

给对象发送一条`retain`消息，引用计数器`+1`，`retain`方法返回对象本身。
给对象发送一条`release`消息， 引用计数器`-1`。
给对象发送`retainCount`消息获得引用计数器值。

### 僵尸对象

所占用内存已经被回收的对象，僵尸对象不能再使用。

### 野指针

指向僵尸对象(不可用内存)的指针。

`EXC_BAD_ACCESS`: 访问了一块坏的内存(已经被回收，不可用的内存)。

OC当中没有空指针异常。

```
[nil release]; // 不会报错
```

<span style="color:#e73751">Tips:</span> 基本数据类型不需要内存管理，OC对象才需要内存管理。

### dealloc方法

当一个对象要被回收，就会自定调用。
一定记得在重写的时候，在最后调用`[super dealloc];`

### 内存管理

只要调用了`alloc`、`new`、`retain`或`copy(mutablecopy)`，必须有`release或autorelease`。

### @property

```
@property (retain) Book *book; // 就可以生成严谨的有内存管理的setter和getter
```

@property括号中的参数

- 内存管理相关的3个参数:
	+ `retain`: 使用于OC对象类型，有`retain`记得要`release`，这个会`release`旧值， 在`setter`中`retain`新值。
	+ `assign`: 直接赋值，适用于非OC对象类型。`(默认)` 出于代码规范，就算是默认值也需要写出来，显式总比隐式清晰明了。
	+ `copy`: `release`旧值，`retain`新值。
- 是否要生成setter:
	+ `readonly`: 不生成setter，只会生成getter
	+ `readwrite`: 同时生成setter和getter。`(默认)`
- 多线程管理:
	+ `nonatomic`: 生成setter和getter时不加锁，性能高，一般就用这个。
	+ `atomic`: 生成setter和getter时加锁，性能低。`(默认)`
- setter和getter方法名称的修改
	+ `setter=setFunc:` set方法一定记得加`:`冒号
	+ `getter=func`

<!-- more -->

代码规范: 返回BOOL类型的方法名，一般以`is`开头，所以返回BOOL值的getter可以写成`getter=isFunc`。

<span style="color:#e73751">Tips:</span> 当两个类循环嵌套引用时，出现内存泄露的解决方案: 一端用`retain`，一段用`assign`。

### Autorelease

autorelease会返回对象本身，返回id类型。
autorelease会将对象放到一个自动释放池中。调用时，不改变引用计数器的值。
当自动释放池(也是对象)被销毁时，会对池子里面的所有对象做一次release操作。

``` objc
Person *p = [[[Person alloc] init] autorelease];
```

自动释放池``@autoreleasepool{}``这是ios5.0之后的创建方式。
代码块开始代表创建了释放池，代码块结束代表销毁了释放池。

autorelease的好处:

+ 不用再关心对象释放的时间。
+ 不用再关心什么时候调用`release`。

autorelease的坏处:

+ 不能精确的控制对象释放的时间。

@autoreleasepool{}本身可以自由嵌套
autorelease不是自动释放，是AutoreleasePool的实例，在runloop中被”稍后“释放。

autorelease的使用注意:

- 占用内存较大的对象不要随便使用`autorelease`。
- 占用内存较小的对象使用`autorelease`没有太大的影响。

错误写法:

- alloc之后调用了autorelease，又调用了release。
- 对同一个对象连续调用多次autorelease。

自动释放池
1. 在`iOS`程序运行过程中，会创建无数个释放池，这些释放池都是以栈结构(弹匣式)存在。
2. 当一个对象调用`autorelease`方式时，会将这个对象放到栈顶的释放池。

<span style="color:#e73751">Tips1:</span> 系统自带的方法里面没有包含`alloc`、`new`、`copy`，说明返回的对象都是`autorelease`的。
<span style="color:#e73751">Tips2:</span> 被`retain`过的属性，必须在`dealloc`方法中`release`该属性。

### ARC

ARC是自动引用计数，是`LLVM 3.0`编译器特性。

ARC的判断准则: 强引用保证对象的存在，或没有强引用，则对象被自动销毁

指针在ARC中分为`2`种:

- 强指针: 默认情况下，所有的指针都是强指针`__strong`。
- 弱指针: 用`__weak`修饰的指针。

ARC特点:

1. 不允许调用`release`、`retain`、`retainCount`、`autorelease`。
2. 允许重写`dealloc`，但是不允许调用``[super dealloc];``。
3. `@property`的参数:
	- strong 强指针 适用于`OC`对象类型 引用计数器`+1`。
	- weak 弱指针 适用于`OC`对象类型 如果指向的对象被释放，其指向`nil`，可以有效避免野指针。
	- assign 适用于非`OC`对象类型 赋值特性，不涉及引用计数。

iOS类中的属性声明规律:

- `UI`控件用`weak`
- `OC`对象用`strong`
- 基本数据类型用`assign`
- `NSString`用`copy`
- `Delegate`用`weak`

<span style="color:#e73751">Tips:</span> 循环嵌套引用时，一端用`strong`，一端用`weak`。

## @class

@class仅仅是告诉编译器，当前声明的是一个类。
嵌套引用的时候用到`@class 类名;`，写在`.h`文件当中，不用`#import`。

开发中引用一个类的规范:

1. 在`.h`文件中用@class来声明类。
2. 在`.m`文件中用`#import`来包含类所有的声明。这样，只有等到实现文件真正要用到的时候，才会去查看引用类。

使用`@class`的好处及其与`#import`的区别:

1. `@class`可以解决循环引用的问题，而`#import`就会出现循环引用问题。
2. `@class`可以提高性能，用`#import`时，这个类修改后，所有引用这个类的类都需要重新编译一遍。
