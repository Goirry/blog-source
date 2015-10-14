title: "Objective-C 语法(三) Category和SEL"
date: 2015-08-08 16:43:13
categories: OC
tags: [OC,Objective-C]
---

## 分类 Category

分类可以给某一个类扩充一些方法（在不修改原来类代码的基础上），很适合做类的拓展。

### 声明: 

```
@interface 类名 (分类名)
@end
```

### 实现:

```
@implementation 类名 (分类名)
@end
```

### 分类的作用：
在不改变原来类内容的基础上，可以为类增加一些代码。

### 分类注意点:
1.分类只能增加方法，不能增加成员变量。
2.分类方法实现中可以访问原类中的声明的成员变量。
3.分类可以重写原类中的方法。
4.方法调用的优先级: 分类 -> 原类 -> 父类

当程序启动的时候，就会加载一次项目中所有的类，类加载完毕后就会调用``+ load``方法。当第一次使用这个类的时候，就会调用一次``+ initialize``方法。
先加载父类，再加载子类。先初始化父类，再初始化子类。
分类必定在原类之后加载，最后参与编译的分类具有最高优先级。

<!-- more -->

## SEL

SEL其实是对方法的一种包装，将方法包装成一个`SEL类型`的数据，所以SEL是一种数据类型。

``` objc
Person *p = [[Person alloc] init];
[p test];
```

调用方法时，其实有3个步骤:
1. 把test先包装成SEL类型的数据。
2. 根据SEL数据找到对应的内存中的方法地址。
3. 根据方法地址调用对应的方法。

``` objc
[p performSelector:@selector(test)]; // 同样也是调用test方法
```

`performSelector:`是一个对象方法，返回`id`类型数据。要求传入`SEL数据`的参数
`@selector`就返回一个`SEL数据`，传入方法名作为参数就可以把方法名包装成`SEL数据`。

```
// 以下两个方法是同样效果
[p test2:@"123"];
[p performSelector:@selector(test2:) withObject:@"123"];
```

<span style="color:#ff5c16">注意:</span>`test2:`方法名后面的`:`冒号不能少，冒号也是方法名的一部分，表示后面需要传参。

我们常常说的发消息就是发送SEL类型的消息。

``` objc
NSString *funcName = @"test3";
SEL s = NSSelectorFromString(funcName); // 把字符串包装成SEL数据
[p performSelector:s];
```

每个方法内部都有个`_cmd`的SEL数据，代表着当前方法。

``` objc
NSString *str = NSStringFromSelector(_cmd); // 把SEL转成字符串
```

`_cmd`就等于`@selector(当前方法名)`。

## NSLog

使用NSLog打印OC对象，使用`%@`占位符。
使用`NSLog和%@`打印对象时，会调用对象的`- description`方法。
然后拿到`- description`返回值(NSString *)显示。
`- description`默认返回值是地址(句柄)，`- description`就等同于`Java`中的`toString`方法。
在``- description``中写``NSLog(@"%@", self);``会进入死循环。

NSLog输出C语言字符串(%s)时，不能有中文，否则不打印输出结果，printf则不会有这样的问题。

NSLog输出增强

``` objc
NSLog(@"%d", __LINE__); // 打印行号
NSLog(@"%s", __FILE__); // 打印文件路径
NSLog(@"%s", __func__); // 打印当前方法名
```