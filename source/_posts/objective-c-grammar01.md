title: "Objective-C 语法(一) 基本知识"
date: 2015-04-22 18:19:41
categories: OC
tags: [OC,Objective-C]
---
首先说明一下，这个Objective-C语法系列是笔者个人的学习笔记，所讲述的语法不会像语法书当中那么详细，只是一些备忘的知识点。
由于本人自身情况，短时间内学习的语言较多，怕各种语法纠杂在一起，梳理不清，特此记录。如果有朋友对此感兴趣，大家共勉学习。

## OC概念
在C语言的基础上，增加了一层最小的面向对象语法。
可以在OC代码中混入C语言代码，甚至是C++代码。

## 关键字
>### 基本所有的关键字都以@开头

```
@interface、@implementation、@end 
@public、@protected、@private、@selector 
@try、@catch、@throw、@finally  
@protocol、@optional、@required、@class
@property、@synthesize、@dynamic
self、super、id、_cmd、__block、__strong、__weak
```

## 基本数据类型

### int 整型
- 8进制输出 : ``%#o``
- 16进制输出 : ``%#x``

### float 单精度浮点型
表示需要在浮点数后面加`f`，如: ``12.5f``

- ``%e`` 科学计数法 以标准指数形式输出单双精度数，数字部分小数位数为`6`位
- ``%f`` 浮点计数法 以小数形式输出单双精度数，默认输出`6`位小数
- ``%g`` NSLog根据指数的值进行判断是使用浮点计数法或是科学计数法。小于`-4`或大于`5`，采用``%e``，否则采用``%f``

<!-- more -->

### double 双精度浮点型
默认所有的小数都是``double``类型

输出和``float``一样

### char 字符型
``%c`` 输出单个字符

### BOOL 布尔值
- ``YES`` 整型输出为`1` 只有`1`等同于``YES``
- ``NO``  整型输出为`0`

### 限定词
``long, long long, short, unsigned, signed``

- **long** : ``%li``	加`L`表示: ``1.2e+7L``
- **long long** : `%lli` 
- **short** : ``%hi``, ``%ho``, ``%hx``
- **unsigned** 加`U`表示: ``0x00ffU``

## NSLog与printf的区别
``NSLog``接收`OC字符串`作为参数，`printf`接收`C语言字符串`作为参数
``NSLog``输出后会自动换行，``printf``输出后不会自动换行

使用``NSLog``需要 ``#import <Foundation/Foundation.h>``
使用``printf``需要 ``#include <stdio.h>``

## 编译执行程序指令
1. 编译: ``cc -c main.m``
2. 链接: ``cc main.o -framework Foundation``
3. 运行: ``./a.out``

<span style="color:#e73751">Tips:</span> 编译加链接:``cc main.m -framework Foundation``

