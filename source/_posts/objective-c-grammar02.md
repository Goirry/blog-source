title: "Objective-C 语法(二) 类"
date: 2015-04-23 17:28:41
categories: OC
tags: [OC,Objective-C]
---

类是面向对象语言中相当重要的一个知识点，也是最常用的知识点。

## 类的概念
> OC当中的类其实原理就是C中的结构体

> OC中创建一个类需要有两个步骤：类的声明、类的实现

## 类的声明和实现
 - 类的声明：`@interface 类名 : 父类(继承类)`
	大多都继承``NSObject``(基类)
 - 类的实现：`@implementation 类名`

类的声明和类的实现都必须以``@end``结束

``` objc
//类的声明 继承NSObject
@interface Car : NSObject
{//声明对象的属性 成员变量默认会初始化为0
	//@public可以让外部的指针间接访问对象内部的成员变量
	@public
	int _wheels;//类的声明里的成员变量不允许初始化赋值,只能用来声明
	double _speed;
}
//只要是OC对象的方法,必须以减号 - 开头
//OC方法中任何数据类型都必须用小括号()括住
//OC方法中的小括号(): 只用来括住数据类型
- (void) run;
@end

//类的实现 用来实现类的方法
@implementation Car
//方法的实现
- (void) run
{
	NSLog(@"调用了run方法");
}

@end

int main()
{
	//利用类来创建对象或者调用类的方法 [行为执行者 行为名称]
	//new出的对象得到的都是指针，必须用对应类型的指针来指向
	Car *myCar = [Car new];
	//用指针调用对象的属性，赋值或取值
	myCar->wheels = 4;
	myCar.speed  = 100;
	NSLog(@"车子有%d个轮子, 时速是%fkm/h", myCar->wheels, myCar->speed);
	[myCar run];//OC对象只能通过指针来操作 给myCar所指向对象发送一条run消息
	return 0;
}
```

> 任何OC对象都是通过指针来操控的。

> 每一个对象中都有一个`isa`指针指向对应的类，通过对象调用类中方法时，就是顺着`isa`指针找到类中的方法。

成员变量是每个对象都有一份，方法则是共用同一份
成员变量和方法都不能用`static`等关键字修饰，不要跟C语言弄混淆

<!-- more -->

## 对象方法
1. `-`号开头
2. 只能由对象调用
3. 对象方法中能访问当前对象的成员变量

## 类方法
1. `+`号开头
2. 只能由类调用
3. 类方法中不能访问成员变量

<span style="color:#e73751">Tips:</span> 可以允许类方法和对象方法同名

### 类方法的好处和使用场合
- 不依赖于对象，执行效率高
- 能用类方法尽量用类方法

场合: 当方法内部不需要使用到成员变量时，就可以改为类方法

## 方法声明和调用

### 方法声明

``` objc
- (double)addNumber1:(double)number1 andNumber2:(double)number2;
```

`-`号说明是对象方法，`-`号后面的`(double)`是方法返回值类型，``addNumber1:andNumber2:``是方法名，`:`号后面的`(double)`是参数的类型，`number1`和`number2`则是形参。

<span style="color:#ff5c16">注意:</span> 方法如果有参数，则方法名一定不能少了`:`号，否则找不到方法。

### 方法调用

``` objc
[calc addNumber1:10 andNumber2:20];
```

`calc`是计算器对象,调用了``addNumber1:andNumber2:``方法，`10`和`20`则是实参。

## 方法和函数的区别
1. OC方法的声明必须写在``@interface``和``@end``之间，只能实现在``@implementation``和``@end``之间。
	也就是说OC方法不能独立存在，依赖类的存在。
	C的函数独立存在，函数能写在文件的任意位置(类的声明里除外)。
2. OC方法归类所有
3. 函数能写在文件中的任意位置(类的声明里除外),函数归文件所有
4. 函数调用不依赖对象,由于函数和类一点关系都没有,所以函数不能直接通过成员变量名或方法名去访问调用类的成员变量和方法

没有``@interface``，只有``@implementation``，也能成功定义一个类。(弱语法)

``@implementation``中不能声明和``@interface``一样的成员变量。(重复定义)

<span style="color:#e73751">Tips:</span> 成员变量的命名规范:一定要以下划线`_`开头,和其他变量区别开来，系统默认生成和官方代码书写都是以`_`开头的。

## 构造方法
自定义构造方法规范

1. 一定是对象方法，一定以`-`号开头。
2. 返回值一般是id类型。
3. 方法名一般以`init`开头。

`alloc`是类方法，作用是分配存储空间。
`init`是对象方法，作用是构造方法、初始化。
这两个方法合起来就是new方法，不过一般都适用``[[类名 alloc] init]``来进行创建初始化对象。

## static 关键字

使用`static`关键字可以声明变量为静态变量
静态变量*只在程序开始执行时初始化一次*，并一直存储在内存当中，哪怕修饰的是局部变量也一样。

<span style="color:#ff5c16">注意:</span> 成员变量/方法`不能用static关键字`修饰，而C语言是可以的修饰变量和函数的，不要弄混。

## self 指针

概念：指向了当前对象(方法调用者)
所以谁调用方法，self就是谁。
当前类的对象调用对象方法，self就是当前类的对象。
当前类的子类对象调用对象方法，self就是当前类的子类对象。
当前类调用类方法，self就是当前类。

``self->成员变量``可以访问当前对象内部的成员变量
``[self 方法名]``可以调用当前对象的对象方法

## super

``[super 方法名];`` 在子类重写方法中调用父类的方法。

使用场合: 子类重写父类`idea方法`时想保留父类的一些行为。

## 多态

多态：父类指针指向子类对象

<span style="color:#e73751">Tips:</span> OC是单继承的。

## 点语法

点语法的本质还是方法调用(`getter`和`setter`),不是直接访问调用成员变量。
没有`getter`和`setter`则不能使用点语法。

<span style="color:#ff5c16">注意:</span> OC中，为了防止`self.age`误认为是访问成员变量`age`，所有的成员变量，推荐使用`_age`.

## 修饰词
- ``@public`` 在任何地方都能直接访问对象的成员变量。
- ``@private`` 只能在当前类的对象方法中访问，但在子类当中还是有，只是不能直接访问。
- ``@protected`` 能再当前类和子类的对象方法中直接访问（默认修饰）。
- ``@package`` 只要出在同一个框架中，就能直接访问对象的成员变量。

在类的实现中定义成员变量一定是私有的，因为实现是写在`.m`文件中得，其他文件不能加载包含`.m`文件。加上``@public``也没用，因为访问不到。

## @property和@synthesize
`@property` 可以自动生成某个成员变量的`setter`和`getter`声明。
```
@property int age;
```

`@synthesize` 自动生成成员变量的`setter`和`getter`实现，并且会访问`_age`这个成员变量。
```
@synthesize age = _age;
```

`@synthesize age`去实现`@property int age`

` = _age`去访问成员变量`_age`来填充`setter`和`getter`

如果成员变量不存在(没有在`.h`文件中创建)，就会在`@implemention`中自动生成`@private`的成员变量。

最简写法: 只在`@interface`中写``@property int age;``就会自动生成成员变量及其`setter`和`getter`,不用再写@synthesize了。

<span style="color:#e73751">Tips:</span> 自动生成的成员变量是`@protected`，并且如果自己写了`setter`或`getter`则所有自动生成的全都不生成。

## 重写
重写 `- init`方法
重写时，一定要先调用`self = [super init]`方法去初始化父类中得成员变量。
