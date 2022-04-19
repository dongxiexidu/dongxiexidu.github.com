---
layout: post
title: SwiftUI之@AppStorage和@SceneStorage
date: 2021-06-03
tags: SwiftUI
---

### 1.@AppStorage定义
```swift
@available(iOS 14.0, macOS 11.0, tvOS 14.0, watchOS 7.0, *)
@frozen @propertyWrapper public struct AppStorage<Value> : DynamicProperty {

    public var wrappedValue: Value { get nonmutating set }

    public var projectedValue: Binding<Value> { get }
}
```

### 2.@SceneStorage定义
```swift
@available(iOS 14.0, macOS 11.0, tvOS 14.0, watchOS 7.0, *)
@frozen @propertyWrapper public struct SceneStorage<Value> : DynamicProperty {

    public var wrappedValue: Value { get nonmutating set }

    public var projectedValue: Binding<Value> { get }
}
```
### 3.区别
两者定义一样,**但SceneStorage它只在Views中能被获取到。**
**AppStorage是一个全局的存储，它是使用UserDefaults来做持久化的，所以我们可以在app中任何地方获取使用它。**

本质上就是使用属性包装器@propertyWrapper对UserDefault的封装

如果使用了投影属性projectedValue,那么会实现双向绑定

示例:
```swift
struct ContentView: View {
    @AppStorage("message") var message:String = "";
    var body: some View{
        VStack{
            Text("本地存储数据：\(message)")
                .foregroundColor(.blue)
            TextField("请输入要存储的信息",text:$message)
                .textFieldStyle(DefaultTextFieldStyle())
                .padding(10)
                .offset(x: 30, y: 0)
            Button("存储"){
                self.message = "按钮存储信息"
            }
        }
    }
}
```


### AppStorage如何使用
```swift
struct TestSwiftUIApp: App {
    @AppStorage(newUserKey)var newUser: Bool = true
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
取值
```swift
struct ContentView: View {
    @AppStorage(newUserKey) var newUser: Bool = true
    var body: some View{
        VStack{
            let text: String = (newUser == true) ? "1" : "0"
            Text("当前显示是\(text)")
            Button("点击修改值"){
                newUser = !newUser
            }
        }
    }
}
```
实践证明:在ContentView中`@AppStorage(newUserKey) var newUser: Bool = true`并不会覆盖之前的值

当然也可以使用强制解包声明
```swift
@AppStorage(newUserKey) var newUser: Bool!
```
