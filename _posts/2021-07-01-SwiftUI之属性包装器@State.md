---
layout: post
title: SwiftUI之属性包装器@State
date: 2021-07-01
tags: SwiftUI
---


### 1.什么是 @State 属性包装器？
Swift 5.1 引入的新关键词，官方的定义有些抽象

```swift
A persistent value of a given type, through which a view reads and monitors the value.
```
一个给给定类型的持久化值，通过这个值view可以读取并监控这个数值。

用大白话讲，@State就是一个标签，贴之前视图是不可以修改这个值;
贴了之后，只要你修改这变量，界面就会跟着同步修改。这个是现代界面语言都是支持的特性。

```swift
struct ContentView: View {
    @State // 如果不贴标签会报错 Left side of mutating operator isn't mutable: 'self' is immutable
    private var tapCount = 0
    var body: some View {
        Button("Tap count: \(tapCount)") {
            tapCount += 1
        }
    }    
}
```   
这在视图中创建了一个属性，但是它使用 @State 属性包装器来要求 SwiftUI 管理内存。
这一点非常重要，**我们所有的视图都是结构体，这意味着它们不能被改变**，如果甚至不能在应用程序中修改一个整数，那么我们也做不了什么。
所以，当我们说 @State 来创建属性时，把它的控制权交给 SwiftUI，
这样只要视图存在，它就会一直存在于内存中；
当状态发生变化时，SwiftUI 会自动重新加载带有最新变化的视图，这样它就可以反映它的新信息。
 
### 2.属性包装器@State使用场景
@State 对于属于特定视图且永远不会在该视图之外使用的简单属性非常有用
因此，** 将这些属性标记为 private 非常重要** ，以强化这样一种想法，即此类状态是专门设计的，永远不会逃逸其视图。

### 3.属性包装器@State原理
@State 修饰的值，在 SwiftUI 内部会被自动转换为一对setter 和 getter，
对这个属性进行赋值的操作将会触发 View 的刷新，
它的 body 会被再次调用，底层渲染引擎会找出界面上被改变的部分，根据新的属性值计算出新的 View，并进行刷新。

### 4.为什么属性包装器@State只适用于结构体?
SwiftUI 的 State 属性包装器是为当前视图本地的简单数据而设计的，但是一旦您想在视图之间共享数据，它就不再有用了。
示例:
```swift
struct User {
    var firstName = "Bilbo"
    var lastName = "Baggins"
}

struct ContentView: View {
    @State private var user = User()

    var body: some View {
        VStack {
            Text("Your name is \(user.firstName) \(user.lastName).")

            TextField("First name", text: $user.firstName)
            TextField("Last name", text: $user.lastName)
        }
    }
}
```
可以看到程序一切正常，SwiftUI 足够聪明，可以理解一个对象包含所有数据，并且会在任一值更改时更新 UI。
在背后，实际发生的情况是，每次结构中的值发生变化时，整个结构都会发生变化，每次键入名字或姓氏的键时，
就像一个新用户，这样听起来可能很浪费，但实际上非常快。

 User 结构体更改为一个类，如下：
```
struct User {}

// -->
class User {}
```

现在再次运行程序，会发生什么呢？
可以看到，它不再起作用了，当然还可以像以前一样在文本字段中输入，但上面的文本视图不会改变。
当使用@State 时，我们要求 SwiftUI 观察属性的变化。
因此，如果改变一个字符串，改变一个布尔值，添加到一个数组等，属性已经改变，SwiftUI 将重新调用视图的 body 属性。

当 User 是一个结构体时，每次修改该结构体的属性时，Swift 实际上都是在创建该结构体的一个新实例。
@State 能够发现这种变化，并自动重新加载视图。现在我们改成了一个类，这种行为不再发生：Swift 可以直接修改值。

还记得我们是如何对修改属性的结构体方法使用 mutating 关键字的吗？
这是因为如果我们将结构体的属性创建为变量，但结构体本身是常量，无法更改属性，
Swift 需要能够在属性更改时销毁并重新创建整个结构体，而这对于常量结构；
类不需要 mutating 关键字，因为即使类实例被标记为常量 Swift 仍然可以修改变量属性。
 
>>类似Java中的注解
