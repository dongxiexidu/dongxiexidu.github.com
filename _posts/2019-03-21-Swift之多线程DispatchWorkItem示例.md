---
layout: post
title: Swift之多线程DispatchWorkItem示例
date: 2019-03-21
tags: Swift
---

### 异步线程执行任务,结束后回到主线程比较常用的做法GCD函数
```swift
// 执行异步任务
DispatchQueue.global().async {
    
    // 回到主线程执行任务,比如刷新UI
    DispatchQueue.main.async {
        //...
    }
}
```

DispatchWorkItem也可以做到,而且更优雅美观,封装示例如下

```swift
// 自定义封装多线程开发
struct DXAsync {
    
    public typealias Task = () -> Void
    
    public static func async(_ task: @escaping Task) {
        _async(task)
    }
    
    public static func async(_ task: @escaping Task, mainTask: @escaping Task) {
        _async(task, mainTask: mainTask)
    }
    
    
    /// 私用方法
    /// - Parameters:
    ///   - task: 异步任务
    ///   - mainTask: 异步任务执行后,回到主线程的任务
    private static func _async(_ task: @escaping Task, mainTask: Task? = nil) {
        let workitem = DispatchWorkItem.init(block: task)
        DispatchQueue.global().async(execute: workitem)
        if let tempTask = mainTask {
            workitem.notify(queue: DispatchQueue.main, execute: tempTask)
        }
        
    }
    
    // 多线程开发-异步延迟
    @discardableResult
    public static func asyncDelay(seconds: Double,
                                    task: @escaping Task) -> DispatchWorkItem {
        
        return _asyncDelay(seconds: seconds, task: task)
    }
    
    @discardableResult
    public static func asyncDelay(seconds: Double,
                                    task: @escaping Task,
                                    mainTask: @escaping Task) -> DispatchWorkItem {
        
        return _asyncDelay(seconds: seconds, task: task, mainTask: mainTask)
    }
    
    
    
    private static func _asyncDelay(seconds: Double,
                                    task: @escaping Task,
                                    mainTask: Task? = nil) -> DispatchWorkItem {
        let workItem = DispatchWorkItem.init(block: task)
        DispatchQueue.global().asyncAfter(deadline: .now() + seconds, execute: workItem)
        if let tempTask = mainTask {
            workItem.notify(queue: DispatchQueue.main, execute: tempTask)
        }
        return workItem
    }
    
}
```

1.异步函数使用示例
```swif
DXAsync.async {
    print("1当前线程是",Thread.current)
}

DXAsync.async({
    print("2当前线程是",Thread.current)
}, mainTask: {
    print("3当前线程是",Thread.current)
})

// 打印
1当前线程是 <NSThread: 0x600000fa2d00>{number = 6, name = (null)}
2当前线程是 <NSThread: 0x600000f96f00>{number = 3, name = (null)}
3当前线程是 <NSThread: 0x600000fd1700>{number = 1, name = main}
```

2.异步延迟示例
```swift
// 全局变量
var workItem: DispatchWorkItem?
    
workItem = DXAsync.asyncDelay(seconds: 3, task: {
    print("1当前线程是",Thread.current)
}, mainTask: {
    print("2当前线程是",Thread.current)
})

//1当前线程是 <NSThread: 0x600003d84640>{number = 5, name = (null)}
//2当前线程是 <NSThread: 0x600003dd2c80>{number = 1, name = main}
```
#### 为什么要有返回值?

因为是有延迟调用,所以这个异步任务可以取消,**但是主线程中的任务仍然会执行**
```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    // 取消异步任务,但是主线程的任务还是会执行
    workItem?.cancel()
}
// 仅执行主线程的任务
//2当前线程是 <NSThread: 0x6000037f1000>{number = 1, name = main}
```

