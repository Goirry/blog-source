title: "Foundation基础"
date: 2015-08-11 18:28:12
categories: iOS
tags: [iOS,OC,Foundation]
---

首先，`Foundation框架`是iOS开发当中的基本框架，所以以下的知识点都是iOS开发中最最基础的。

## 结构体

- `NSRange(location, length)`
- `NSPoint/CGPoint(x, y)`
- `NSSize/CGSize(width, height)`
- `NSRect/CGRect(CGPoint, CGSize)`

``` objc
NSString *str = @"Hello iOS,This is a string.";
NSRange range = [str rangeOfString:@"OS"];
if (range.location == NSNotFound) { // NSNotFound等于-1
	NSLog(@"字符串找不到");
} else {
	NSLog(@"匹配字符串在查找字符串的位置:%ld, 长度:%ld", range.location, range.length);
}

// 初始化写法
CGPoint point = NSMakePoint(10, 20);
CGPoint point1 = CGPointMake(20, 25);
NSSize size = CGSizeMake(50, 100);
CGRect rect = CGRectMake(0, 20, 50, 80);
CGRect rect1 = {CGPointZero, CGSizeMake(200, 200)};

NSString *strPoint = NSStringFromCGPoint(point); // 输出:{10, 20}

CGPointZero == CGPointMake(0, 0); // 常用原点

// 比较
CGPointEqualToPoint(point, point1);
CGRectEqualToRect(rect, rect1); // 其他写法均类似 就不一一列举
```

## 字符串

- `NSString` 不可变字符串

``` objc
//初始化写法
NSString *string1 = @"123"; // 常用
NSString *string2 = [[NSString alloc] initWithString:@"string123"]; // 繁琐, 一般不用
NSString *string3 = [[NSString alloc] initWithFormat:@"Age = %d", 99];

// C语言字符串->OC字符串
NSString *ocString1 = [[NSString alloc] initWithUTF8String:"C language string"];
NSString *ocString2 = [[NSString alloc] stringWithUTF8String:"C language string"];

// OC字符串->C语言字符串
const char *cString = [@"OC string" UTF8String];

// 读取文件内容
NSString *fileString = [[NSString alloc] initWithContentsOfFile:@"/Users/Guo/Document/1.txt" encoding:NSUTF8StringEncoding error:nil];
```

- `NSMutableString` 可变字符串

``` objc
// NSMutableString是NSString的子类 方法继承
NSMutableString *mutableStr = [NSMutableString stringWithFormat:@"age is 10"];
// 追加字符串
[mutableStr appendString:@" 11 12"];

NSRange range = [mutableStr rangeOfString:@" is"];
// 删除范围内的字符串
[mutableStr deleteCharactersInRange:range];

NSString *unchangeableString = [NSString stringWithFormat:@"age is 10"];
// 重新创建出一个新的字符串 并且基于原字符串追加
NSString *unchangeableString1 = [unchangeableString stringByAppendingString:@" 11 12"];
```

<!--more-->

## URL资源路径

URL格式: `资源头://路径`

- 本地资源路径: `file://`
- 网络资源路径: `http://`、`ftp://`等

``` objc
// 定义URL 也相当于NSString->NSURL
NSURL *url = [[NSURL alloc] initWithString:@"file:///Users/Guo/Document/1.txt"];
// 定义文件地址URL 直接传入文件地址
NSURL *fileUrl = [NSURL fileURLWithPath:@"/Users/Kevin_Guo/Document/1.txt"];

// NSURL->NSString
NSString *urlPath = url.path;

// 通过URL读取文件内容
NSString *str = [[NSString alloc] initWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
// 通过文件路径读取文件内容
NSString *fileContentStr = [NSString stringWithContentsOfFile:@"Users/Guo/Document/1.txt" encoding:NSUTF8StringEncoding error:nil];
```

## 集合类
* 数组
	+ 有序
		- `NSArray` 不可变
		- `NSMutableArray` 可变
	+ 无序
		- `NSSet` 不可变
		- `NSMutableSet` 可变
* 字典
	- `NSDictionary` 不可变
	- `NSMutableDictionary` 可变

> OC当中任何集合类都不能存放`非OC对象`，比如`基本数据类型`、`struct`、`enum`等。
> 也不能存放空值`nil`。

``` objc
//--------------NSArray------------
int ages[10] = {5, 55, 5, 5, 5, 8}; // C数组

// OC数组不能存放非OC对象类型 比如基本类型, struct, enum等  OC数组不能存放nil值
// 初始化空数组 并且这个数组永远是空的 不可变
NSArray *arr = [NSArray array];

NSArray *array1 = [NSArray arrayWithObject:@"jack"];
NSArray *array2 = [NSArray arrayWithObjects:@"jack", @"rose", nil]; // nil是数组元素结束的标记
    
NSLog(@"数组个数:%ld",[array2 count]);//数组个数
    
NSLog(@"第二个数组元素:%@", [array2 objectAtIndex:1]);
NSLog(@"%@", array2[1]);// 编译器特性 会自动转换成上面的代码

// NSArray的便捷初始化方法 编译器特性
NSArray *array3 = @[@"jack", @"rose", @"sam"];

//遍历数组
for(id obj in array3) {
    NSLog(@"数组元素的序号:%ld", [array3 indexOfObject:obj]);
    NSLog(@"数组元素:%@", obj);
}
// 迭代循环 遍历数组
[array2 enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    NSLog(@"序号:%ld --> 值:%@", idx, obj);
    if (idx == 0) {
        *stop = YES; // 停止可控
    }
}];

// 数组拼接成字符串
NSString *stringFromArray = [array3 componentsJoinedByString:@"-"];
NSLog(@"%@", stringFromArray); // 打印结果:jack-rose-sam

// 把字符串拆分成数组
NSArray *arrayFromString = [stringFromArray componentsSeparatedByString:@"-"];
for (id obj in arrayFromString) {
    NSLog(@"%@", obj);
}


//----------NSMutableArray-----------

// 初始化空的可变数组
NSMutableArray *mutableArr = [NSMutableArray array];
NSMutableArray * mutableArr1 = [NSMutableArray arrayWithObjects:@"jack", @"rose", @"jim", nil];
    
[mutableArr addObject:@"string"]; // 可变数组添加元素
[mutableArr count]; // 数组元素数量
[mutableArr removeAllObjects]; // 删除数组中所有元素
[mutableArr removeObject:@"jack"]; // 删除数组中指定对象
[mutableArr removeObjectAtIndex:1]; // 按照序号删除数组   数组中删除前面元素 后面元素也会自动前移


//-------------NSSet----------------
NSSet *set = [NSSet set]; // 创建一个空Set
NSSet *set1 = [NSSet setWithObjects:@"jack", @"rose", @"sam", nil];
NSLog(@"set1:%@", set1); // 打印整个Set
NSLog(@"set1的元素个数:%ld", set1.count);
NSLog(@"set随机取元素:%@", [set1 anyObject]);


//-----------NSMutableSet------------
NSMutableSet *mSet = [NSMutableSet set];
[mSet addObject:@"jack"]; // 添加元素
[mSet removeObject:@"jack"]; // 删除元素


//-------------NSDictionary--------------
NSDictionary *dict = [NSDictionary dictionary]; // 空字典
NSDictionary *dict1 = [NSDictionary dictionaryWithObject:@"Jack" forKey:@"key"];
NSDictionary *dict2 = @{@"key1": @"value1", @"key2": @"value2", @"key3":@"value3"}; // NSDictionary的便捷初始化方法
NSLog(@"key1的值:%@", [dict2 objectForKey:@"key1"]); // 通过key取value
NSLog(@"key2的值%@", dict2[@"key2"]); // 便捷的通过key取value 编译器特性
NSLog(@"dict2:%@", dict2); // 打印整个字典
[dict2 count];// 字典中键值对的数量
[dict2 allKeys]; // 取到字典当中所有的key

// 遍历字典
[dict2 enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
    NSLog(@"NSDictionary:%@-->%@", key, obj);
}];


//-----------NSMutableDictionary----------------
NSMutableDictionary *mDict = [NSMutableDictionary dictionary];
// 没有同名Key的时候做添加 有同名Key的时候做修改
[mDict setObject:@"value" forKey:@"key"];
NSLog(@"key的值%@", mDict[@"key"]);
[mDict removeObjectForKey:@"key"];// 移除键值对
```

## NSNumber

`NSNumber`只能包装数字类型，NSNumber之所以能包装基本数据类型对象是因为继承了NSValue。

``` objc
NSNumber *num = [NSNumber numberWithInt:10]; // int -> NSNumber 转换
// 基本数据类型 --> OC对象类型 转换的便捷写法 编译器特性
NSNumber *num1 = @20.0;
NSNumber *num2 = @YES;
@'A'; // 字符 -> NSNumber对象 值为A的ASCII码值65
int num3 = 12;
@(num3); // 变量 -> NSNumber对象

// OC集合类中都不能存放非OC对象 所以10必须包装成对象类存放进字典
NSDictionary *dictionary = @{@"number": @"jack", @"age": num};
// NSNumber --> int 转换
int a = [num intValue];
// NSNumber --> double 转换
int b = [num1 doubleValue];
NSString *strNum = [NSString stringWithFormat:@"%d", a];
// NSString --> int 转换
[strNum intValue];
```

## NSValue

`NSValue`才是最终的包装类，才真正具备包装成对象的功能，而且不管是什么类型，都能包装。

``` objc
// 结构体 --> OC对象类型 NSValue
NSValue *value1 = [NSValue valueWithPoint:CGPointMake(10, 10)];
NSValue *value2 = [NSValue valueWithSize:CGSizeMake(200, 200)];
NSArray *arr1 = @[value1, value2];
// NSValue --> 结构体
[value1 pointValue]; 
[value2 sizeValue];
```

## NSDate

`NSDate`是时间类，OC中时间单位是秒，不是毫秒。Java才是毫秒，PHP的时间单位也是秒。

``` objc
// 创建当前时间
NSDate *date = [NSDate date];
NSLog(@"0时区的时间是:%@",date);
// 创建出比现在时间晚5秒的时间
NSDate *date1 = [NSDate dateWithTimeInterval:5 sinceDate:date];
// 返回时间的秒值
NSTimeInterval seconds = [date1 timeIntervalSince1970];

// ---日期格式化---
// 初始化时间格式类
NSDateFormatter *fomatter = [[NSDateFormatter alloc] init];
// 用字符串的形式设置时间格式
[fomatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];
// NSDate --> NSString 转换
NSLog(@"格式化时间:%@", [fomatter stringFromDate:date]);

// NSString --> NSDate 转换
NSString *time = @"2011/09/10 15:24";
[fomatter setDateFormat:@"yyyy/mm/dd HH:mm"];
NSDate *date2 = [fomatter dateFromString:time];
NSLog(@"订制时间:%@", date2);
    
// 日期类 初始化格林高利日期
NSCalendar *calendar = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];

// NSDateComponents表示时间时,必须添加单位,否则取出的单位时间会是错误的数值
NSDateComponents *components = [calendar components:NSYearCalendarUnit|NSMonthCalendarUnit|NSDayCalendarUnit|NSHourCalendarUnit|NSMinuteCalendarUnit|NSSecondCalendarUnit fromDate:date];
components.hour = 0;
components.minute = 0;
components.second = 0;
NSDate *newDate = [calendar dateFromComponents:components];
NSLog(@"Today:%@", newDate);

// 根据两个时间点，定义NSDateComponents对象，从而获取这两个时间点的时差
NSDateComponents *dateComponents = [calendar components:NSYearCalendarUnit fromDate:[NSDate dateWithTimeIntervalSince1970:0] toDate:[NSDate date] options:0];
NSLog(@"number of years:%i", dateComponents.year);
```