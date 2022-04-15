---
layout: post
title: SwiftUI之属性包装器@EnvironmentObject
date: 2021-07-04
tags: SwiftUI
---


### 1.什么是 @EnvironmentObject?
- 我们已经看到 @State 如何为一个类型声明简单的属性，当它改变时会自动导致视图刷新，以及@ObservedObject 如何为一个外部类型声明一个属性，当它改变时可能会也可能不会导致视图刷新，这两者都必须由视图设置，但 @ObservedObject 可能与其它视图共享。
- 还有另一种类型的属性包装器可供使用，它是 @EnvironmentObject，这是一个通过应用程序本身提供给视图的值，它是每个视图都可以读取的共享数据。因此，如果应用程序有一些所有视图都需要读取的重要模型数据，可以将它从一个视图传递到另一个视图，或者只是将其放入每个视图都可以即时访问它的环境中。
- 当需要在应用程序周围传递大量数据时，@EnvironmentObject 将带来巨大的便利，因为所有的视图都指向同一个模型，如果一个视图改变了模型，所有的视图都会立即更新，没有让应用程序的不同部分出现不同步的风险。
- SwiftUI 的 @EnvironmentObject 属性包装器，可以创建依赖于共享数据的视图，通常跨整个 SwiftUI 应用程序。 例如，如果创建一个将在应用程序的许多部分共享的用户，则应使用@EnvironmentObject。例如，我们可能有一个像这样的 Order 类：
```swift
class Order: ObservableObject {
    @Published var items = [String]()
}
```
- 这符合 ObservableObject，这意味着可以将它与 @ObservedObject 或 @EnvironmentObject 一起使用。在这种情况下，我们可能会创建一个使用@EnvironmentObject 的视图，如下所示：
```swift
struct ContentView: View {
    @EnvironmentObject var order: Order

    var body: some View {
        // your code here
    }
}
```
- 请注意 order 属性是如何没有给出默认值的，通过使用 @EnvironmentObject，我们说该值将由 SwiftUI 环境提供，而不是由该视图显式创建。
- @EnvironmentObject 与@ObservedObject 有很多共同点：两者都必须引用一个符合 ObservableObject 的类，两者都可以在多个视图之间共享，并且都将在发生重大变化时更新任何正在观察的视图。但是，@EnvironmentObject 特指此对象将从某个外部实体提供，而不是由当前视图创建或专门传入。
- 实际上，想象一下如果您有视图 A，并且视图 A 有一些视图 E 想要的数据，使用@ObservedObject 视图 A 需要将对象交给视图 B，视图 B 将把它交给视图 C，然后是视图 D，最后是视图 E，所有中间视图都需要发送对象，即使它们实际上并没有需要它。
- **使用 @EnvironmentObject 时，视图 A 可以创建一个对象并将其放入环境中；然后，其中的任何视图都可以随时通过请求访问该环境对象，而不必显式传递它**，这样会使我们的代码更简单。
- 当显示使用 @EnvironmentObject 的视图时，SwiftUI 将立即在环境中搜索正确类型的对象，如果找不到这样的对象，即如果你忘记把它放在环境中，那么你的应用程序将立即崩溃。当您使用 @EnvironmentObject 时，实际上是在保证对象将在需要时存在于环境中，有点像使用隐式解包选项。

### 三者区分
- @State 用于属于单个视图的简单属性，它们通常应标记为私有。
- @ObservedObject 用于可能属于多个视图的复杂属性，大多数情况下，如果使用的是引用类型，那么应该为它使用 @ObservedObject。
- 对使用的每个可观察对象使用 @StateObject 一次，无论代码的哪个部分负责创建它。
- @EnvironmentObject 用于在应用程序中其它地方创建的属性，例如共享数据。
