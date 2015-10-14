title: "Objective-C 语法(五) Block和Protocol"
date: 2015-08-10 18:39:24
categories: OC
tags: [OC,Objective-C]
---

## Block

block用来保存一段代码，可以写在函数内部，可以运行时保存一段代码(函数做不到)。

block的标志: `^`

block和函数的相同点:

1. 可以保存代码。
2. 有返回值。
3. 有形参。
4. 调用方式一样。

定义block变量:

- 无参数、无返回值 ``void (^BlockName)();``
- 有参数、有返回值 ``int (^BlockName)(int, int)``

利用block封装代码:

``` objc
^() {
	NSLog(@"Hello!");
};

// 无参可以省略括号, 如下:
^{
	NSLog(@"Hello!");
};

// 有参
^(int a, int b) {
	return (a + b);
}

int (^MinusBlock)(int, int) = ^(int a, int b) {
	return (a - b);
}

MinusBlock(5, 3); // 调用
```

block可以访问外部的变量。默认情况下，block内部不能修改外部的局部变量。给外部的局部变量加上`__block`关键字，就可以在block内部进行修改了。

``` objc
__block int sum = 10;

int (^MyBlock)(int) = ^(int num) {
	sum++;
	return (num * sum);
};
```

<!-- more -->

可以利用typedef定义block变量:

``` objc
typedef int (^MySum)(int, int);

MySum sumBlock = ^(int a, int b) {
	return (a + b);	
};
```

<span style="color:#e73751">Tips:</span> 苹果官方建议尽量多使用block。在多线程、异步任务、集合遍历、集合排序、动画转场用的很多。

## Protocol

可以用来声明一大堆方法，但是不能声明成员变量。
只要某个类遵守了这个协议，就相当于拥有这个协议中的所有方法声明。
只要父类遵守了某个协议，就相当于子类也遵守了该协议。

某个类遵守协议:

``` objc
// 声明协议 与#import相比，"ProtocolName.h"性能更好，理由与@class相似
@protocol ProtocolName; 

//定义遵守协议的类 支持多协议 <协议1, 协议2, 协议3>
@interface ClassName : SuperClass <ProtocolName> 
@end
```

协议中的修饰关键字:
`@required` 要求实现的方法，不实现会发出警告。`(默认)`
`@optional` 不要求实现的方法，可选性实现。

``` objc
// 以下的func1和func2是要求实现的，func3是可选实现的。
@required
- (void) func1;
- (void) func2;

@optional
- (void) func3;
```

协议使用的场合:
最多的是用在代理模式、观察者模式上。

基协议:
`NSObject`是一个基类(最根本最基本的类)，还有一个协议，名字也叫`NSObject`，是基协议。建议每个新的协议都要遵守NSObject协议。

``` objc
// 基本协议的定义
@protocol ProtocolName <NSObject>

// 方法声明

@end
```

类的属性声明中:

``@property (nonatomic, strong) id<MyProtocol> obj;``表明所有赋值给obj的对象都一定要遵守MyProtocol协议。

``` objc
// 要求保存对象的指针 保存的对象必须是遵守MyProtocol协议的
id<MyProtocol> obj = [[Person alloc] init];
```