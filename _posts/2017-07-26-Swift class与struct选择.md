---
layout: post
title: Swift class与struct选择
date: 2017-07-26
categories: Swift
tags: Swift关键字
---
在Swift中`class`和`struct`关系其实很简单,类似`UIView`与`CALayer`,我们知道类`class`的本质就是结构体,所以说结构体`struct`相比较类`class`更加轻量级,因为类的功能比结构体多很多

**结构体`struct`是值类型,类`class`是引用类型**
通过一个示例来演示值类型与引用类型,数据之间的传递

### 引用类型模型数据传递示例
```swift
// 定义一个模型类,用来存放数据
class Model: NSObject {
    var age: Int = 0
}
// 在控制器A中
class ViewController: UIViewController {

    var model: Model = Model()
    
    @IBAction func jumpAction(_ sender: UIButton) {
        let vc = DetailController()
        vc.model = self.model
        self.navigationController?.pushViewController(vc, animated: true) 
    }
    // 打印当前模型的值和地址
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        print(model.age)
        print(model)
    }
}    
```
在控制器B中
```Swift
class DetailController: UIViewController {

    var model: Model?
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        if let models = model {
            models.age = models.age + 1
            print(models.age)
            print(models)
        }
    }    
```

### 问题
跳转到控制器B中,点击屏幕`model.age+1`,那么age由最初的0变成1,那么返回控制器A,点击屏幕此时`model.age`是什么呢?当model是class类型时候,此时是引用类型,那么我们在控制器B操作的模型数据实际上就是对控制器A中的`model`的操作,这就是引用类型,引用的对象是同一个

如果model修改为结构体类型,那么在控制器B中,这段代码就会报错
```Swift
struct Model {
    var age: Int = 0
}
```
![demo]({{ "/assets/img/structError.png" | absolute_url }})

报错很明显,因为这里`model`使用了`let`修饰,结构体`model`变成了常量,`model.age`也变成了常量

需要修改为如下
```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {

    model!.age = model!.age + 1
    print(model!.age)
    print(model)
}
```
当我们再次点击控制器B的屏幕,可以看到输出`1,2,3`,返回控制器A,打印当前`model.age`值仍然是0.
通过这个示例,如果model是引用类型,那么是地址传递,查看地址一模一样

### 结构体指针的打印查看
在控制器A中
```
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    print(model.age)
    let userPointer =  withUnsafePointer(to: &model, {$0})
    print(userPointer)
}    
0
0x00007f9ebcd07de0
```

在控制器B中
```
1
0x00007f9ebcd1efd0
```
显而易见,指针地址都不同

## 类和结构体的不同点
- 1.类可以继承,结构体不能继承
- 2.类可以调用deinit方法(结构体没有deinit 方法),释放任何资源分配
- 3.类有引用计数,允许对象被多次引用
- 4.类能够在运行时检查和解释类实例的类型


## 什么时候用结构体
- 1.该结构的主要目的是封装几个相对简单的数据值
- 2.如果你希望你的结构在传递的时候被赋值而不是引用
- 3.希望结构在传递的时候,内部的属性也被复制而不是引用
- 4.不需要**继承**属性或者方法

官方建议:
>define a class, and create instances of that class to be managed and passed by reference. In practice, this means that most custom data constructs should be classes, not structures.

虽然官方建议尽量使用类而不是结构体,因为类更加灵活,使用更加方便,可以说结构体有的功能类都可以实现,类有限功能结构体无法实现

由于结构体更加轻量级,性能略好点(1.占用内存更少;2.创建结构体实例速度更快),所以在项目实际使用的时候优先虑使用结构体(特别是数据量比较大的时候),当发现结构体无法满足项目需求的时候,才更改使用类

参考[酷走天涯: Swift3.0 - 类和结构体的区别](https://www.jianshu.com/p/51f99a352838)
