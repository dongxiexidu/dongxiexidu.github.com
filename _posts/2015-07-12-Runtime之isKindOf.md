---
layout: post
title: Runtime之isKindOf
date: 2015-07-12
tags: Runtime
---

面试题如下
```swift
NSLog(@"%d", [[NSObject class] isKindOfClass:[NSObject class]]);
NSLog(@"%d", [[NSObject class] isMemberOfClass:[NSObject class]]);
NSLog(@"%d", [[MJPerson class] isKindOfClass:[MJPerson class]]);
NSLog(@"%d", [[MJPerson class] isMemberOfClass:[MJPerson class]]);
```
`[NSObject class]`的本质就是类对象,所以以上写法等同于如下写法
```swift
NSLog(@"%d", [NSObject isKindOfClass:[NSObject class]]); 
NSLog(@"%d", [NSObject isMemberOfClass:[NSObject class]]); 
NSLog(@"%d", [MJPerson isKindOfClass:[MJPerson class]]); 
NSLog(@"%d", [MJPerson isMemberOfClass:[MJPerson class]]);
```


### 打印结果
```swift
[NSObject isKindOfClass:[NSObject class]]=1
[NSObject isMemberOfClass:[NSObject class]]=0
[MJPerson isKindOfClass:[MJPerson class]]=0
[MJPerson isMemberOfClass:[MJPerson class]0
```
### 为啥结果跟预期的想法视乎不一致,先看以下题目

```swift
id person = [[MJPerson alloc] init];

NSLog(@"%d", [person isMemberOfClass:[MJPerson class]]);
NSLog(@"%d", [person isMemberOfClass:[NSObject class]]);

NSLog(@"%d", [person isKindOfClass:[MJPerson class]]);
NSLog(@"%d", [person isKindOfClass:[NSObject class]]);


NSLog(@"%d", [MJPerson isMemberOfClass:object_getClass([MJPerson class])]);
NSLog(@"%d", [MJPerson isKindOfClass:object_getClass([NSObject class])]);
```
### 它们的底层实现
```swift
#import <objc/runtime.h>

- (BOOL)isMemberOfClass:(Class)cls {
    return [self class] == cls;
}

- (BOOL)isKindOfClass:(Class)cls {
    for (Class tcls = [self class]; tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}

+ (BOOL)isMemberOfClass:(Class)cls {
    return object_getClass((id)self) == cls;
}

+ (BOOL)isKindOfClass:(Class)cls {
    for (Class tcls = object_getClass((id)self); tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}
```
### 总结:
- 1.如果是对象的比较(-开头的方法),那么右边应该传类对象进行比较,他们本质就是左边传来的实例对象的类对象与右边的类对象进行比较
```swift
// // 同理isKindOfClass
- (BOOL)isMemberOfClass:(Class)cls {
    // 左边传来的实例对象的类对象与右边的类对象进行比较
    return [self class] == cls;
}
```
- 2.如果是类对象的比较(+开头的方法),那么右边应该传元类对象进行比较
```swift
// 同理isKindOfClass
+ (BOOL)isMemberOfClass:(Class)cls {
    // 获取左边的类对象的元类,那么右边也应该是元类对象的比较
    return object_getClass((id)self) == cls;
}
```

```swift
// 这句代码的方法调用者不管是哪个类（只要是NSObject体系下的），都返回YES
NSLog(@"%d", [NSObject isKindOfClass:[NSObject class]]);
```


再看以下面试题,给`NSObject`写一个分类,声明一个实例方法,一个类方法,仅仅实现实例方法
```swift
@interface NSObject (Interview)
+ (void)foo;
- (void)foo;
@end

@implementation NSObject (Interview)
- (void)foo{
    NSLog(@"- (void)foo");
}
@end
```
那么调用,`NSObject`类方法,会发生崩溃?
```swift
[NSObject foo];
```
答案是不会发生崩溃,而且调用了对象方法.
### 原理:根据消息机制,会先在**元类**查找`foo`方法,没找到,`NSObject`会一层一层往上查找`tcls = tcls->superclass`,最终`NSObject`元类通过`superclass`会找到`NSObject`类对象,类对象存储着对象方法,正是`foo`方法,于是就找到
```swift
- (BOOL)isKindOfClass:(Class)cls {
    for (Class tcls = [self class]; tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}
```

![demo]({{ "/assets/img/superclass.png" | absolute_url }})
