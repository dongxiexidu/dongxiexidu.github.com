---
layout: post
title: Runtime之super面试题
date: 2015-07-11
tags: Runtime
---

面试题如下

1.父类`DXPerson`继承自`NSObject`
```swift
#import <Foundation/Foundation.h>
// 声明
@interface DXPerson : NSObject

@end

@implementation DXPerson

@end
```


2.子类`DXStudent`继承父类`DXPerson`
```swift
@interface DXStudent : DXPerson

@end

@implementation DXStudent
- (instancetype)init
{
    if (self = [super init]) {
        NSLog(@"[self class] = %@", [self class]); 
        NSLog(@"[self superclass] = %@", [self superclass]); 

        NSLog(@"--------------------------------");
        
        NSLog(@"[super class] = %@", [super class]); 
        NSLog(@"[super superclass] = %@", [super superclass]); 
    }
    return self;
}
@end
```
前两个很容易猜测出来,后两个如果凭感觉来,很多人就会出错,`[super class] = DXPerson`,`[super superclass] = NSObject`


### 实际打印结果
```swift
[self class] = DXStudent
[self superclass] = DXPerson
--------------------------------
[super class] = DXStudent
[super superclass] = DXPerson
```
转成C++代码,查看`[super class]`底层实现
```swift
struct objc_super {
    __unsafe_unretained _Nonnull id receiver; // 消息接收者
    __unsafe_unretained _Nonnull Class super_class; // 消息接收者的父类
};

struct objc_super arg = {self, [DXPerson class]};
objc_msgSendSuper({self, [DXPerson class]}, @selector(class));

// super调用的receiver仍然是DXStudent对象
// super指明,查找class方法从父类DXPerson类对象开始查找,而不是从本身DXStudent类对象
[super class]
```
### `class`和`superclass`的都是`NSObject`的方法,它们的底层实现
```swift
#import <objc/runtime.h>

// 伪代码
- (Class)class{
    return object_getClass(self);
}
// 伪代码
- (Class)superclass{
    return class_getSuperclass(object_getClass(self));
}
```
### 总结:[super message]的底层实现
- 1.消息接收者仍然是子类对象
- 2.从父类开始查找方法的实现
