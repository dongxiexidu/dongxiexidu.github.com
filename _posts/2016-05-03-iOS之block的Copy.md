---
layout: post
title: iOS之Block的copy
date: 2018-10-01
tags: block
---

### 在ARC环境下,block作为函数的返回值,编译器会默认进行copy操作,因此block不会销毁
```swift
typedef void(^DXBlock)(void);
DXBlock dxBlock() {
    // 在ARC环境下,block作为函数的返回值,编译器会默认进行copy操作,因此block不会销毁
    DXBlock block = ^ {
        NSLog(@"=====");
    };
    return block;
}
```
如果在MRC环境下,下面这段代码会崩溃,因为block已经释放
```swift
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        DXBlock block =dxBlock();
        block();
        // __NSGlobalBlock__
        NSLog(@"%@",[block class]);
    }
}
```

面试题:如果捕获了局部变量,如下代码block是什么类型
```swift
typedef void(^DXBlock)(void);
DXBlock dxBlock() {
    int height = 10;
    // 在ARC环境下,block作为函数的返回值,编译器会默认进行copy操作,因此block不会销毁
    DXBlock block = ^ {
        // 捕获了外部变量
        NSLog(@"height=%d",height);
    };
    return block;
}

DXBlock block =dxBlock();
block();
NSLog(@"%@",[block class]);
```
答案是`__NSMallocBlock__`


### 将block赋值给_strong指针时候,会自动copy

```swift
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        int height = 10;
        // 将block赋值给_strong指针
        DXBlock block = ^ {
            NSLog(@"height=%d",height);
        };
        block();
        NSLog(@"%@",[block class]); // __NSMallocBlock__
    }
    return 0;
}
```

如下,没有强指针
```swift
int height = 10;
NSLog(@"%@",[^{
    NSLog(@"height=%d",height);
} class]); // __NSStackBlock__
```

### block作为Cocoa API中方法名含有`usingBlock`的方法参数时候,block会进行copy操作
```swift
NSArray *array = @[];
// block会进行copy操作,放在堆上,命比较久
[array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    
}];
```
### block作为GCD API的方法参数时,block会进行copy操作
```swift
// block会进行copy操作,放在堆上,命比较久
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

});
```
在MRC下,`block`属性的写法
```swift
// 使用copy,进行保命
@property (nonatomic,copy) void(^block)(void);
```
在ARC下,`block`属性用`copy`或者`strong`一样
```swift
@property (nonatomic,copy) void(^block)(void);
@property (nonatomic,strong) void(^block)(void);
```
