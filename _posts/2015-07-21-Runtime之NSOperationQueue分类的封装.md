---
layout: post
title: Runtime之NSOperationQueue分类的封装
date: 2015-07-21
tags: Runtime 队列
---

- 1.使用runtime给NSOperationQueue分类添加block属性`completionBlock`
- 2.使用KVO监听私有属性`operationCount`
- 3.使用方法交换`dealloc`

头文件`NSOperationQueue+CompletionBlock.h`

```swift
#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

@interface NSOperationQueue (CompletionBlock)
// 这个是由NSOperationQueueOperationCountObserver对象持有
@property (strong, nonatomic, nullable) void(^completionBlock)(void);

@end

NS_ASSUME_NONNULL_END
```

NSOperationQueue+CompletionBlock.m文件
```Swift
#import "NSOperationQueue+CompletionBlock.h"
#import <objc/runtime.h>

@interface NSOperationQueueOperationCountObserver : NSObject

@property (strong, nonatomic, nullable) void(^completionBlock)(void);

@end

@implementation NSOperationQueueOperationCountObserver

// KVO监听属性operationCount的变化,如果队列添加了多个操作(比如addOperationWithBlock)
// 那么这个回调将会多次调用
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change
                       context:(void *)context{
    
    if ([keyPath isEqualToString:@"operationCount"]) {
        NSInteger operationCount = [change[NSKeyValueChangeNewKey] integerValue];
        if (operationCount == 0 && self.completionBlock) {
            self.completionBlock();
        }
    }
}

@end

@implementation NSOperationQueue (CompletionBlock)

#pragma mark Swizzle Dealloc

+ (void)load{
    [super load];
    
    method_exchangeImplementations(class_getInstanceMethod(self.class, NSSelectorFromString(@"dealloc")), class_getInstanceMethod(self.class, NSSelectorFromString(@"swizzledDealloc")));
}
- (void)swizzledDealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    if (self.completionBlock) {
        [self removeObserver:self.observer forKeyPath:@"operationCount"];
    }
    [self swizzledDealloc];
}

#pragma mark operationCounterObserver

// 队列计数器监听者:这个对象持有completionBlock,
- (nonnull NSOperationQueueOperationCountObserver *)observer{
    NSOperationQueueOperationCountObserver *observer = objc_getAssociatedObject(self, _cmd);
    if (!observer) {
        observer = [[NSOperationQueueOperationCountObserver alloc] init];
        objc_setAssociatedObject(self, @selector(observer), observer, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    }
    return observer;
}

#pragma mark completionBlock
- (nullable void(^)(void))completionBlock {
    return self.observer.completionBlock;
}

// 通过completionBlock的set方法添加observer监听NSOperationQueue的私有属性operationCount值的变化
// 通过observer对completionBlock进行保存
- (void)setCompletionBlock:(nullable void (^)(void))completionBlock{
    if (completionBlock) {
        [self addObserver:self.observer forKeyPath:@"operationCount" options:NSKeyValueObservingOptionNew context:NULL];
    } else {
        [self removeObserver:self.observer forKeyPath:@"operationCount"];
    }
    self.observer.completionBlock = completionBlock;
}

@end
```
使用示例
```Swift
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    
    [queue addOperationWithBlock:^{
        // currentThread<NSThread: 0x6000028fde40>{number = 3, name = (null)}
        NSLog(@"currentThread%@",[NSThread currentThread]);
        [NSThread sleepForTimeInterval:2];
    }];
    
    queue.completionBlock = ^{
        // currentThread<NSThread: 0x6000028fde40>{number = 3, name = (null)}
        NSLog(@"currentThread%@",[NSThread currentThread]);
    };
}
```
