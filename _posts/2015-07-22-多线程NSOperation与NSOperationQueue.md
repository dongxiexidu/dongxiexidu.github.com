---
layout: post
title: 多线程NSOperation与NSOperationQueue
date: 2015-07-22
tags: 多线程
---

**NSOperation、NSOperationQueue 简介**:NSOperation、NSOperationQueue 是苹果提供给我们的一套多线程解决方案,NSOperation、NSOperationQueue 是基于 GCD 更高一层的封装，完全面向对象。但是比 GCD 更简单易用、代码可读性也更高

### NSOperation 是个抽象类(定义规范),类似C++中的抽象类,不能直接使用,要使用它的子类
- 1.使用子类 NSInvocationOperation
- 2.使用子类 NSBlockOperation
- 3.自定义继承自 NSOperation 的子类，通过实现内部相应的方法来封装操作。

### NSOperation 实现多线程的使用步骤分为三步：
- 1.创建操作：先将需要执行的操作封装到一个 NSOperation子类对象中。
- 2.创建队列：创建 NSOperationQueue 对象。
- 3.将操作加入到队列中：将 NSOperation 对象添加到 NSOperationQueue 对象中。


由此我们可以看出,NSOperation(操作或者叫任务)与NSOperationQueue(队列)的关系,他们是组合使用的关系

实际上他们是可以拆分使用的
### NSOperation的基本使用
- 1.使用子类`NSInvocationOperation`

在主线程中调用
```Swift
/// 使用子类 NSInvocationOperation
- (void)useInvocationOperation {

    // 1.创建 NSInvocationOperation 对象
    NSInvocationOperation *op = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(task1) object:nil];

    // 2.调用 start 方法开始执行操作
    [op start];
}


- (void)task1 {
    for (int i = 0; i < 2; i++) {
        [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
        NSLog(@"1---%@", [NSThread currentThread]); // 打印当前线程
    }
}
```
输出结果：
```swift
1---<NSThread: 0x6000035c2940>{number = 1, name = main}
1---<NSThread: 0x6000035c2940>{number = 1, name = main}
```


在其他线程中执行操作，则打印结果为其他线程。
```swift
// 在其他线程使用子类 NSInvocationOperation
[NSThread detachNewThreadSelector:@selector(useInvocationOperation) toTarget:self withObject:nil];
```
输出结果
```swift
1---<NSThread: 0x600000dfcbc0>{number = 3, name = (null)}
1---<NSThread: 0x600000dfcbc0>{number = 3, name = (null)}
```
**小结**:在主线程中单独使用使用子类 NSInvocationOperation 执行一个操作的情况下，操作是在当前主线程执行的，并没有开启新线程,如果开启了新的线程,那么操作任务就是在其他线程


- 2.使用子类 NSBlockOperation
```Swift

// 使用子类 NSBlockOperation
- (void)useBlockOperation {

    // 1.创建 NSBlockOperation 对象
    NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"1---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];

    // 2.调用 start 方法开始执行操作
    [op start];
}
```
输出结果：
```Swift
 1---<NSThread: 0x600003ff2180>{number = 1, name = main}
 1---<NSThread: 0x600003ff2180>{number = 1, name = main}
 ```
在没有使用 NSOperationQueue、在主线程中单独使用 NSBlockOperation 执行一个操作的情况下，操作是在当前线程执行的，并没有开启新线程

和刚刚的`NSInvocationOperation`一样,在子线程中调用操作任务,同样会在子线程中执行
```Swift
[NSThread detachNewThreadSelector:@selector(useInvocationOperation) toTarget:self withObject:nil];
```
输出结果：
```Swift
1---<NSThread: 0x600002a36400>{number = 3, name = (null)}
1---<NSThread: 0x600002a36400>{number = 3, name = (null)}
```
## 在单独使用NSBlockOperation时候,添加到blockOperationWithBlock的操作任务,一定是当前线程执行吗?

换句话说,在主线程中,添加blockOperationWithBlock的操作任务,一定是在主线程中执行吗?

示例代码如下,在主线程中添加操作,并且执行
```Swift
- (void)useBlockOperationAddExecutionBlock {
    
    // 1.创建 NSBlockOperation 对象
    NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i < 4; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"1---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    
    // 2.添加额外的操作
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"2---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"3---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"4---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"5---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"6---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"7---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2]; // 模拟耗时操作
            NSLog(@"8---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    
    // 3.调用 start 方法开始执行操作
    [op start];
}
```
打印输出:
```swift
1---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
4---<NSThread: 0x600000da9400>{number = 1, name = main}
2---<NSThread: 0x600000de3740>{number = 4, name = (null)}
3---<NSThread: 0x600000dfb100>{number = 3, name = (null)}
4---<NSThread: 0x600000da9400>{number = 1, name = main}
1---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
3---<NSThread: 0x600000dfb100>{number = 3, name = (null)}
2---<NSThread: 0x600000de3740>{number = 4, name = (null)}
6---<NSThread: 0x600000da9400>{number = 1, name = main}
7---<NSThread: 0x600000de3740>{number = 4, name = (null)}
1---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
5---<NSThread: 0x600000dfb100>{number = 3, name = (null)}
1---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
6---<NSThread: 0x600000da9400>{number = 1, name = main}
7---<NSThread: 0x600000de3740>{number = 4, name = (null)}
5---<NSThread: 0x600000dfb100>{number = 3, name = (null)}
8---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
8---<NSThread: 0x600000dc8000>{number = 5, name = (null)}
```
**小结**:如果添加的操作多的话，blockOperationWithBlock: 中的操作也可能会在其他线程（非当前线程）中执行，这是由系统决定的，**并不是说添加到 blockOperationWithBlock: 中的操作一定会在当前线程中执行**

一般情况下，如果一个 `NSBlockOperation` 对象封装了多个操作。NSBlockOperation 是否开启新线程，取决于操作的个数。如果添加的操作的个数多，就会自动开启新线程。当然开启的线程数是由系统来决定的

### 队列NSOperationQueue的基本使用(和操作NSOperation的组合使用)

因为队列单独使用是没有意义的,也就是说队列只能配合操作使用,默认情况可以创建一个队列,在队列中直接添加block操作,而不需要专门独立创建操作
```Swift
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
// 添加到队列的任务,默认会执行,不需要像NSOperation那样,调用 start 方法才开始执行操作
[queue addOperationWithBlock:^{
    // currentThread<NSThread: 0x6000028fde40>{number = 3, name = (null)}
    NSLog(@"currentThread%@",[NSThread currentThread]);
    [NSThread sleepForTimeInterval:2];
}];
```
注意:**添加到队列的任务,默认会执行,不需要像NSOperation那样,调用 start 方法才开始执行操作**

NSOperationQueue 一共有两种队列：主队列、自定义队列。其中自定义队列同时包含了串行、并发功能

凡是添加到主队列中的操作，一般情况都会放到主线程中执行（**注：不包括操作使用addExecutionBlock:添加的额外操作，额外操作可能在其他线程执行**)
```Swift
// 主队列获取方法
NSOperationQueue *queue = [NSOperationQueue mainQueue];
```
自定义队列（非主队列）
- 添加到这种队列中的操作，就会自动放到子线程中执行。
- 同时包含了：串行、并发功能

```Swift
// 自定义队列创建方法
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
```

### 当前线程是主线程下,凡是添加到主队列中的操作,一定都会放到主线程中执行吗?
示例代码如下
```Swift
- (void)addOperationToQueue {
    
    // 1.创建队列
  //  NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    NSOperationQueue *queue = [NSOperationQueue mainQueue];
    
    // 2.创建操作
    // 使用 NSInvocationOperation 创建操作1
    NSInvocationOperation *op1 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(task1) object:nil];
    
    // 使用 NSInvocationOperation 创建操作2
    NSInvocationOperation *op2 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(task2) object:nil];
    
    // 使用 NSBlockOperation 创建操作3
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:1]; // 模拟耗时操作
            NSLog(@"3---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    [op3 addExecutionBlock:^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:1]; // 模拟耗时操作
            NSLog(@"4---%@", [NSThread currentThread]); // 打印当前线程
        }
    }];
    
    // 3.使用 addOperation: 添加所有操作到队列中
    [queue addOperation:op1]; // [op1 start]
    [queue addOperation:op2]; // [op2 start]
    [queue addOperation:op3]; // [op3 start]
}
- (void)task1 {
    for (int i = 0; i < 2; i++) {
        [NSThread sleepForTimeInterval:1]; // 模拟耗时操作
        NSLog(@"1---%@", [NSThread currentThread]); // 打印当前线程
    }
}
- (void)task2 {
    for (int i = 0; i < 2; i++) {
        [NSThread sleepForTimeInterval:1]; // 模拟耗时操作
        NSLog(@"2---%@", [NSThread currentThread]); // 打印当前线程
    }
}
```

输出:
```Swift
1---<NSThread: 0x600002e8e940>{number = 1, name = main}
1---<NSThread: 0x600002e8e940>{number = 1, name = main}
2---<NSThread: 0x600002e8e940>{number = 1, name = main}
2---<NSThread: 0x600002e8e940>{number = 1, name = main}
4---<NSThread: 0x600002ee4000>{number = 3, name = (null)}
3---<NSThread: 0x600002e8e940>{number = 1, name = main}
3---<NSThread: 0x600002e8e940>{number = 1, name = main}
4---<NSThread: 0x600002ee4000>{number = 3, name = (null)}
```
因此,我们得出结论:在当前线程是主线程情况下,添加到主队列中的操作任务,不一定都是在主线程,也可能是子线程,示例如添加到额外操作`addExecutionBlock`

上面的主队列更改为自定义队列
```Swift
// 自定义队列创建方法
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
```
自定义队列输出:
```Swift
4---<NSThread: 0x600003e523c0>{number = 6, name = (null)}
3---<NSThread: 0x600003e54c80>{number = 3, name = (null)}
2---<NSThread: 0x600003e58ec0>{number = 5, name = (null)}
1---<NSThread: 0x600003e66180>{number = 4, name = (null)}
3---<NSThread: 0x600003e54c80>{number = 3, name = (null)}
2---<NSThread: 0x600003e58ec0>{number = 5, name = (null)}
1---<NSThread: 0x600003e66180>{number = 4, name = (null)}
4---<NSThread: 0x600003e523c0>{number = 6, name = (null)}
```
使用 `NSOperation`子类创建操作，并使用 `addOperation: `将操作加入到操作队列后能够开启新线程，进行并发执行


**总结**: NSOperation 需要配合 NSOperationQueue 来实现多线程
