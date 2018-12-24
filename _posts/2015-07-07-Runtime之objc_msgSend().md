---
layout: post
title: Runtime之objc_msgSend()
date: 2015-07-07
tags: Runtime
---

## Objective-C的方法调用：消息机制，给方法调用者发送消息

`Objective-C`的方法的调用,其实都是转换成`objc_msgSend`函数的调用
```swift
DXPerson *person = [[DXPerson alloc] init];
[person personTest];
```
转换成C++代码,查看底层实现
```swift
((void (*)(id, SEL))(void *)objc_msgSend)((id)person,
sel_registerName("personTest"));
// 去掉强制转换类型的代码后
objc_msgSend(person, sel_registerName(personTest));

// sel_registerName(personTest) == @selector(personTest)
```
- 消息接收者（receiver）：person
- 消息名称：personTest

类方法一样,例如
```swift
[DXPerson initialize];
```
转换成C++代码
```swift
((void (*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("DXPerson"),
sel_registerName("initialize"));
// 去掉强制转换类型的代码后
objc_msgSend(objc_getClass("DXPerson"), sel_registerName("initialize"));
// [DXPerson class] == objc_getClass("DXPerson")
```
### 为什么给nil发消息不会崩溃?
通过`objc_msgSend`源码解读(汇编代码),第一步就是判断`receiver1`是不是为空
```swift
// 伪代码
void objc_msgSend(id receiver, SEL selector){
    if (receiver == nil) {
        return ;
    }
}
```
