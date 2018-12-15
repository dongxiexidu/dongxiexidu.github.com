---
layout: post
title: iOS方法的底层结构method_t
date: 2015-07-05
tags: Runtime
---

`metho_t`是对方法/函数的封装
```swift
struct method_t{
    SEL name; // 方法选择器,函数名,方法名
    const char *types; // C语言字符串, 编码(返回值类型.参数类型)
    IMP imp; // 指向函数的指针(用来存放函数具体地址),指针类型
}
```
IMP代表函数的具体实现
```swift
typedef id _Nullable (*IMP)(id _Nullnull, SEL _Nonnull, ...);
```

SEL代表方法/函数名,一般叫做选择器,底层结构跟char *类似
```swift
typedef struct objc_selector *SEL;
```
可以通过`@selector()`和`sel_registerName()` 获取
可以通过`sel_getName()`和`NSStringFromSelector()`转换成字符串

注意:不同类中相同名字的方法,所对应的方法选择器是相同的
```swift
Person *p = [[Person alloc] init];
SEL sel1 = sel_registerName("test");
SEL sel2 = @selector(test);
NSLog(@"%p %p",sel1,sel2); // 0x10a63b3b1 0x10a63b3b1

[p performSelector:@selector(test) withObject:nil];
```

```swift
Person *p = [[Person alloc] init];
// (v == void) 16 (@代表id类型), 0 (:代表SEL类型) 8
[p test]; 

// 默认传两个参数 test:(id)self _cmd:(SEL)_cmd
- (void)test{
    
}
```
### iOS中提供了一个叫做@encode的指令,可以将具体的类型表示成字符串编码
encode字符编码,为了方便运行时的,把一个方法名,方法参数,返回值通过字符串编码表示
```swift
NSLog(@"%s",@encode(int)); // i
NSLog(@"%s",@encode(id)); // @
NSLog(@"%s",@encode(SEL)); // :
NSLog(@"%s",@encode(IMP)); // ^?
NSLog(@"%s",@encode(Person)); // {Person=#}
```

方法中,`types`源码的解析(返回值类型.参数类型)
```swift
// "i24@0:8i16f20"
// i 表示 int
// 24所以参数所占用的字节 8 + 8 + 4 + 4
// @ 参数是id类型
// 0表示第一个参数@从第0个字节开始算
// :表示参数是SEL类型
// 8表示第二个参数从第8个字节开始算
// i表示 int类型参数
// 16第三个参数从第16个字节开始算
// f表示 float类型参数
// 20第四个参数从第20个字节开始算
- (int)test:(int)age height:(float)height;
```
