---
layout: post
title: FOUNDATION_EXPORT与#define和extern
date: 2015-05-20
tags: 理论
---

定义常量:

### 1.使用`extern`
```swift
.h中可以声明,导入头文件直接使用,也可以不声明,直接在使用这个常量的文件里直接声明
extern NSString * const kMyConstantString;
--------------------------
.m实现
NSString * const kMyConstantString = @"hello";
```
###  2.使用`FOUNDATION_EXPORT`
```swift
.h中可以声明,导入头文件直接使用,也可以不声明,直接在使用这个常量的文件里直接声明
FOUNDATION_EXPORT NSString * const kMyConstantString;
--------------------------
.m实现
NSString * const kMyConstantString = @"Hello";
```
### 3.使用`#define`
```swift
#define kMyConstantStr @"hello"
```

### 区别
- 1.使用`#define`只能使用`isEqual `方法比较是否相等,值比较
- 2.使用`extern`或者`FOUNDATION_EXPORT`,可以使用`==`进行比较是否相等,指针地址比较,使用效率比`#define`更快
- 3.一般来说,`#define`宏定义仅仅是在当前文件(.m文件)使用,`extern`和`FOUNDATION_EXPORT`全局使用
- 4.如果变量声明中带有关键字`extern`,仅仅暗示这个函数肯能在别的源文件里有定义,没有其他作用

![demo]({{ "/assets/img/extern.jpeg" | absolute_url }})

**`extern`不应该用在定义常量**

`extern`修饰的**全局变量**默认是有外部链接的，作用域是整个工程，在一个文件内定义的全局变量，在另一个文件中，通过`extern`全局变量的声明，就可以使用全局变量

`extern`使用,不需要导入头文件,只需要引用
```swift
#import "ViewController.h"
// 这里只是声明,定义(分配内存)是在自定义(Common.m)的类里面进行的
extern NSString * const kMyConstantString;

@interface ViewController ()

@end
```

```swift
- (void)viewDidLoad {
    [super viewDidLoad];
    NSString *myStr = @"hello";
    // 值比较
    if ([myStr isEqual: kMyConstantStr]) {
       ...
    }
    // 指针是否相等
    if (myStr == kMyConstantString) {
       ...
    }
}

```

Xcode的编译器是能够识别C++语言编程代码的
```swift
#if defined(__cplusplus)
#define FOUNDATION_EXTERN extern "C"
#else
#define FOUNDATION_EXTERN extern
#endif
```
**由以上定义可以看出 FOUNDATION_EXTERN 是可以兼容C++的extern的宏。同样也可以推测出 extern "C" 也就是用来兼容C++里面的extetrn 的**


[stackoverflow里- “FOUNDATION_EXPORT” vs “extern”](http://stackoverflow.com/questions/10953221/foundation-export-vs-extern)

总结,使用Objective-C开发,使用`extern`或者`FOUNDATION_EXPORT`是没有区别的.
