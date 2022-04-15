---
layout: post
title: 如何全局获取当前第一响应者
date: 2018-05-18
tags: 理论
---
### 大体思路
- 1.给`UIResponder`扩展分类`UIResponder+FirstResponder`
- 2.声明一个类方法,返回第一响应者
- 3.通过`[[UIApplication sharedApplication] sendAction:@selector(findFirstResponder:) to:nil from:nil forEvent:nil];`获取并赋值给第一响应者
- 4.`[self.nextResponder findSecondResponder:sender];`获取第二响应者


```swift
#import <UIKit/UIKit.h>

@interface UIResponder (FirstResponder)

+ (instancetype)currentFirstResponder;

+ (instancetype)currentSecondResponder;

@end
```

```swift
#import "UIResponder+FirstResponder.h"
static __weak id currentFirstResponder;
static __weak id currentSecodResponder;

@implementation UIResponder (FirstResponder)

+ (instancetype)currentFirstResponder {
    currentFirstResponder = nil;
    currentSecodResponder = nil;
    // Public API 非私有API
    // 通过将target设置为nil，让系统自动遍历响应链
    // 从而响应链当前第一响应者响应我们自定义的方法
    [[UIApplication sharedApplication] sendAction:@selector(findFirstResponder:) to:nil from:nil forEvent:nil];
    return currentFirstResponder;
}

+ (instancetype)currentSecondResponder{
    currentFirstResponder = nil;
    currentSecodResponder = nil;
    [[UIApplication sharedApplication] sendAction:@selector(findFirstResponder:) to:nil from:nil forEvent:nil];
    return currentSecodResponder;
}

- (void)findFirstResponder:(id)sender {
    currentFirstResponder = self;
    [self.nextResponder findSecondResponder:sender];
}

- (void)findSecondResponder:(id)sender{
    currentSecodResponder = self;
}
@end
```


Swift版本
```swift
import UIKit

private weak var wty_currentFirstResponder: AnyObject?

extension UIResponder {

    static func wty_firstResponder() -> AnyObject? {
        wty_currentFirstResponder = nil
        // 通过将target设置为nil，让系统自动遍历响应链
        // 从而响应链当前第一响应者响应我们自定义的方法
        UIApplication.shared.sendAction(#selector(wty_findFirstResponder(_:)), to: nil, from: nil, for: nil)
        return wty_currentFirstResponder
    }
    
    func wty_findFirstResponder(_ sender: AnyObject) {
        // 第一响应者会响应这个方法，并且将静态变量wty_currentFirstResponder设置为自己
        wty_currentFirstResponder = self
    }
}
```

`static`解析

 >全局变量写在哪都还是全局变量
 我在外部使用extern NSString * a;声明一下，它就暴露了，就可以修改它的值
加上static以后表示这变量只能在这个文件里用(.m文件)，外面看不见也改不了

`static`总结:
**修饰局部变量**

- 1、使得局部变量只初始化一次
- 2、局部变量在程序中只有一份内存
- 3、局部变量的作用域不变，但是生命周期变了(直到程序结束才销毁)

**修饰全局变量**
- 1、全局变量的作用域仅限当前文件


`__weak `解析
```swift
__weak specifies a reference that does not keep the 
referenced object alive. A weak reference is set to nil when
there are no strong references to the object.
```
>使用了__weak修饰符的对象，作用等同于定义为weak的property。自然不会导致循环引用问题，因为苹果文档已经说的很清楚，当原对象没有任何强引用的时候，弱引用指针也会被设置为nil。

`__block`解析
```swift
// A powerful feature of blocks is that they can modify 
variables in the same lexical scope. You signal that a block 
can modify a variable using the __block storage type 
modifier. 

// At function level are __block variables. These are mutable
 within the block (and the enclosing scope) and are preserved
if any referencing block is copied to the heap.
```
- 1.__block修饰的对象在block中是可以被修改、重新赋值的。 
- 2.__block修饰的对象在block中不会被block强引用一次，从而不会出现循环引用问题

私有API
```swift
UIWindow *keyWindow = [[UIApplication sharedApplication] keyWindow];
UIView *firstResponder = [keyWindow performSelector:@selector(firstResponder)];
```

参考:[不用私有API，一行代码获取当前响应链的First Responder](https://www.jianshu.com/p/84c0eddf2378)
