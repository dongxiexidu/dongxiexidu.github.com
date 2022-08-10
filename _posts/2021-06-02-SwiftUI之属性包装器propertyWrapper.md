---
layout: post
title: SwiftUI之属性包装器propertyWrapper
date: 2021-06-02
tags: SwiftUI
---

### 什么是@propertyWrapper(属性包装)?
- 1.它类似Java语言中的注解
- 2.它主要用于包装修饰属性的get set方法
- 3.目的在于封装属性操作,简化代码,降低重复书写概率

在 Swift 中，这一特性的正式名称是属性包装 (Property Wrapper)。不论是 @State， @Binding，或者是我们在下一节中将要看到的 @ObjectBinding 和 @EnvironmentObject，它们都是被 @propertyWrapper 修饰的 struct 类型。

以 State 为例，在 SwiftUI 中 State 定义的关键部分如下:
### 1.State定义
```swift
@main
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen @propertyWrapper public struct State<Value> : DynamicProperty {
    public init(initialValue value: Value)
    public init(wrappedValue value: Value)
    public var wrappedValue: Value { get nonmutating set }
    public var projectedValue: Binding<Value> { get }
}
```
init(initialValue:)，wrappedValue 和 projectedValue 构成了一个 propertyWrapper 最重要的部分。

示例:
```swift
struct ContentView : View {

@State //1
private var brain: CalculatorBrain = .left("0")
var body: some View { VStack(spacing: 12) {
      Spacer()
      Text(brain.output) // 2
        //...
      CalculatorButtonPad(brain: $brain) // 3
        //...
} }
}
```
- 1. 由于 init(initialValue:) 的存在，我们可以使用直接给 brain 赋值的写法，将一 个 CalculatorBrain 传递给 brain。我们可以为属性包装中定义的 init 方法添 加更多的参数，我们会在接下来看到一个这样的例子。不过 initialValue 这个 参数名相对特殊:当它出现在 init 方法的第一个参数位置时，编译器将允许我 们在声明的时候直接为 @State var brain 进行赋值。
- 2. 在访问 brain 时，这个变量暴露出来的就是 CalculatorBrain 的行为和属性。 对 brain 进行赋值，看起来也就和普通的变量赋值没有区别。但是，实际上这 些调用都触发的是属性包装中的 wrappedValue。@State 的声明，在底层将 brain 属性 “包装” 到了一个 State<CalculatorBrain> 中，并保留外界使用者 通过 CalculatorBrain 接口对它进行操作的可能性。
- 3. 使用美元符号前缀 ($) 访问 brain，其实访问的是 projectedValue 属性。在 State 中，这个属性返回一个 Binding 类型的值，通过遵守 BindingConvertible，State 暴露了修改其内部存储的方法，这也就是为什么 传递 Binding 可以让 brain 具有引用语义的原因。

### 应用示例:
实际开发中,我们的默一个属性要求必须为两头去除空格的状态.
这时我们有两种方式实现
1, 重写get方法,每次get时都去除两头空格
2, 重写set方法,每次set时都去除两头空格
```swift
class Test {
    var whiteStr {
        set {
            // 去除空格操作
        }
        // 或者
        get {
            // 去除空格操作
        }
    }
}
```
这时我们会面临一个问题,如果有很多个属性都有这个要求
那么我们的代码就会变成
```swift
class Test {
    var whiteStr1 {
        set {
            // 去除空格操作
        }
        // 或者
        get {
            // 去除空格操作
        }
    }
    
    var whiteStr2 {
        set {
            // 去除空格操作
        }
        // 或者
        get {
            // 去除空格操作
        }
    }
    ...
}
```
充其量我们把“去除空格操作”抽象提取出来,但也并不能降低多少代码重复率,还是在不停的写set get

这时@propertyWrapper的作用就提现出来了
```swift
class Test {
    @NoWhite() //“NoWhite”这个名字是我们自己定义的
    var whiteStr1 
    
    @NoWhite()
    var whiteStr2
    
    ...
}
```

@propertyWrapper怎么实现及实践
```swift
@propertyWrapper struct NoWhite {
    // 用来记录被包装属性的值
    // 可以是别的名字,比如model item等,value只是在此demo中的名字
    var value: String
    
    // 包装值,可以把它看成value的计算属性
    // 'wrappedValue' 这个名称不能更改,必须实现
    // 以上一段中的`whiteStr`为例
    // 被NoWhite修饰后, 操作`whiteStr`对应的get set 方法,实际是在操作wrappedValue的
    // get set 方法
    // 这里我们选用在set中处理两头空格的方式
    var wrappedValue: String {
        get { value }
        set {
            let whitespace = NSCharacterSet.whitespacesAndNewlines
            value = newValue.trimmingCharacters(in: whitespace)
        }
    }
    
    //  映射值 (只写get方法)
    //  被修饰的属性在属性名前加`$`符,调用的就是的projectedValue的get方法
    //  比如, self.$whiteStr
    //  当使用self.$whiteStr作为参数传递时,是引用传递. 
    //  使用self.whiteStr作为参数传递时,是值传递
     var projectedValue: String {
        let whitespace = NSCharacterSet.whitespacesAndNewlines
        return value.trimmingCharacters(in: whitespace)
    }
    
    // 初始化方法
    init(wrappedValue initialValue: String = "") {
        value = initialValue
    }

}
```

示例:
```swift
class Test {
    @NoWhite()
    var whiteStr
}

te.whiteStr = " 123 " // 首位有空格

print(te.whiteStr) // "123"

print(te.whiteStr.count) // "3"
```

### 总结

1, @propertyWrapper的本质就是包装修饰属性的set get方法

2, @propertyWrapper的作用就是降低代码重复率

3, @propertyWrapper的应用中的projectedValue在SwiftUI的 @State中, 作为 @Binding 的修饰对象的引用传递来解决多层嵌套UI的状态同步问题.

可参考: 喵神的美元人民币转换

