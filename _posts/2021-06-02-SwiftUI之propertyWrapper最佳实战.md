---
layout: post
title: SwiftUI之propertyWrapper最佳实战
date: 2021-06-02
tags: SwiftUI
---

### 通过UserDefaults自定义@propertyWrapper,用来存储数据
- 1.存储的是模型数组[Task]
- 2.取值的时候,需要对数组中的每一条data进行decode
- 3.仅仅使用了`wrappedValue`这个属性,没有使用projectedValue

### 1.构建属性包装器UserDefaultValue
```swift
@propertyWrapper
struct UserDefaultValue<Value: Codable> {

    let key: String
    let defaultValue: Value

    var wrappedValue: Value {
        get {
            let data = UserDefaults.standard.data(forKey: key)
            let value = data.flatMap { try? JSONDecoder().decode(Value.self, from: $0) }
            return value ?? defaultValue
        }
        set {
            let data = try? JSONEncoder().encode(newValue)
            UserDefaults.standard.set(data, forKey: key)
        }
    }
}
```
### 2.使用示例:
```swift
private let defaultTasks: [Task] = [
    Task(title: "Read SwiftUI Documentation 📚", isDone: false),
    Task(title: "Watch WWDC19 Keynote 🎉", isDone: true),
]

//final修饰符可以防止 类（class）被继承
//需要注意的是，final修饰符只能用于类，不能修饰结构体（struct）和枚举（enum），因为结构体和枚举只能遵循协议（protocol）。
final class UserData: ObservableObject {
    let objectWillChange = PassthroughSubject<UserData, Never>()
    
    @UserDefaultValue(key: "Tasks", defaultValue: defaultTasks)
    var tasks: [Task] {
        didSet { // 一旦发生改变,通知外界
            objectWillChange.send(self)
        }
    }
}

// 插入数据
private func createTask() {
    let newTask = Task(title: self.draftTitle, isDone: false)
    // tasks数组的insert方法会触发UserDefaultValue中wrappedValue的set方法
    self.userData.tasks.insert(newTask, at: 0)
    self.draftTitle = ""
}

    
// 模型数据
struct Task: Equatable, Hashable, Codable, Identifiable {
    let id: UUID
    var title: String
    var isDone: Bool
    
    init(title: String, isDone: Bool) {
        self.id = UUID()
        self.title = title
        self.isDone = isDone
    }
}
```

可参考: SwiftUITodo 项目

