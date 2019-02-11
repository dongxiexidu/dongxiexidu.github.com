---
layout: post
title: Swift 关键字associatedtype解析
date: 2017-07-27
categories: Swift
tags: 代理/协议
---
>从字面上来理解,就是相关类型.意思也就是被associatedtype关键字修饰的变量,相当于一个占位符,不表示具体的类型.具体类型需要让实现的类来指定


通过一个示例来演示
```swift
// 因为结构体`struct`不能继承,又不想使用class类型,所以此处使用了protocol
protocol Food {
    // 协议可以声明变量
    var weight: CGFloat{ get }
}

// 定义一个肉类结构体,遵守Food协议,用于肉食动物
struct Meat: Food {
    var weight: CGFloat{
        return 5.0
    }
}

// 定义一个动物的协议
protocol Animal {
    // 凡是动物都有一个吃Food的方法
    func eat(_ food: Food)
}

// 定义一个Lion,遵守Animal协议,实现Animal的eat方法
struct Lion: Animal {
    func eat(_ food: Food) {

        // 在运行时进行类型检查
        if let meat = food as? Meat {
            print("Lion eat \(meat)")
        } else {
            fatalError("Lion can only eat meat!")
        }
    }
}
```
### 外部调用
```Swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let meat = Meat()
    Lion().eat(meat)
}
```

这样看起来代码没有什么问题,现在我们增加一个蔬菜类型的食物,和一只吃蔬菜的兔子

```swift
// 定义一个蔬菜的结构体,遵守Food协议,用于非肉食动物
struct Vegetables: Food {
    var weight: CGFloat{
        return 2.0
    }
}

struct Rabbit: Animal{
    func eat(_ food: Food) {
        // 在运行时进行类型检查,这种做法不好,也不安全
        if let meat = food as? Vegetables {
            print("Rabbit eat \(meat)")
        } else {
            fatalError("Rabbit can only eat vegetables!")
        }
    }
}
```
假设特定的动物只能吃特定类型的食物,比如兔子只吃蔬菜,不吃肉;狮子只吃肉,不吃蔬菜,所以类型检查非常有必要.上面的写法我们无法控制外界传来的食物参数,所以我们在方法实现的内部进行了类型检查.

外界调用无法控制,如下,我偏偏要给狮子喂蔬菜,编译器没有警告,更没有报错,但是这样运行时程序会崩溃
```swift
let vegetables = Vegetables()
// 这样运行时会崩溃
Lion().eat(vegetables)
```
这不是我们想要的结果,Swift作为一门静态类型语言,类型安全一直是它所标榜的,那么能不能把程序设计成不要在运行时检查,而是在外界调用的时候进行检查

我们想要类似这样的结果:如图
![demo]({{ "/assets/img/associatedtype.png" | absolute_url }})

答案肯定是可以.既然想要限制外界调用动物的`eat`方法参数,那么`Animal`协议的`eat`方法参数就不能写死类型
```Swift
protocol Animal {
    // 声明一个关联类型CompatibleType变量,用来占位的
    // : Food可以省略,一般写法 associatedtype T
    associatedtype CompatibleType: Food
    func eat(_ food: CompatibleType)
   // func eat(_ food: Food)
}
```

那么我们写狮子的结构体,并且遵守协议
```Swift
struct Lion: Animal {
    typealias CompatibleType = Meat
    func eat(_ food: Meat) {
        print("Lion eat \(food)")
    }
}
```
当然也可以省略掉定义类型`typealias CompatibleType = Meat`,只写遵守的协议
```Swift
struct Lion: Animal {
    // typealias CompatibleType = Meat
    func eat(_ food: Meat) {
        print("Lion eat \(food)")
    }
}
```
这样我们就限定了特定动物只能传特定参数,保证了外界调用的安全性

当我们在Animal协议中使用了`associatedtype`,这个`Animal`协议就不能当做独立的类型使用了,必须要使用泛型
如果没有使用`associatedtype`,我们可以给`Animal`扩充方法,如下
```Swift
extension Animal{
    func isDangerous(animal: Animal) -> Bool {
        if animal is Lion {
            return true
        } else {
            return false
        }
    }
}
```
使用`associatedtype`后,上面就会报错
```Swift
// 报错
Protocol 'Animal' can only be used as a generic constraint
because it has Self or associated type requirements
```

给`Animal`扩充方法,应该使用泛型,如下
```Swift
extension Animal{
    func isDangerous<T: Animal>(animal: T) -> Bool {
        if animal is Lion {
            return true
        } else {
            return false
        }
    }
}
```

**总结**:`associatedtype`是为了适配协议方法中,外界传不同参数类型而存在的关键字

再来一个实战项目中的示例
```Swift
//模型
struct Model {
    let age: Int
}
 
//协议，使用关联类型
protocol TableViewCell {
    associatedtype T
    func updateCell(_ data: T)
}
 
//遵守TableViewCell
class MyTableViewCell: UITableViewCell, TableViewCell {
    typealias T = Model
    func updateCell(_ data: Model) {
        // do something ...
    }
}
```
协议这样来使用,是不是很规范?是不是很方便?是不是很清晰?

关联类型为协议中的某个类型提供了一个占位名(或者说别名),其代表的实际类型在协议被采纳时才会被指定
