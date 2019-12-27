---
layout: post
title: Swift 如何实现类似kingfisher点语法2
date: 2018-08-30
categories: test
tags: Swift
---

1.声明一个类`RoundCorner `
```swift
public final class RoundCorner<Base> {
    
    public let base: Base
    public init(_ base: Base){
        self.base = base
    }
}
```

2.声明一个空协议,协议名`RoundCornerCompatible`
```swift
public protocol RoundCornerCompatible {

}
```
3.在协议扩展中,添加一个只读属性`dx`,返回一个`RoundCorner `对象
```swift
public extension RoundCornerCompatible {
    public var dx: RoundCorner<Self> {
        get { return RoundCorner(self) }
    }
}
```

4.将所需要实现`.dx`语法的`Base`类遵守`RoundCornerCompatible`协议,按照以往经验,遵守协议,实现协议的方法,但是Swift可以给协议添加可选属性(类似可选方法),凡是遵守这个协议`RoundCornerCompatible`的类,便拥有了.dx这个属性.
这个.dx这个属性实际上是我们之前自定义的`RoundCorner`的一个泛型实例.
```swift
extension UIView : RoundCornerCompatible {}
```

到现在为止,凡是UIView的子类,便可以使用`.dx`,而这个`.dx`实际上就是`RoundCorner`这个实例对象,下面给这个对象添加自定义的方法
5.在`RoundCorner`的扩展中,添加定义的方法
```swift
extension RoundCorner where Base: UIView{
    /// 设置一个四角圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    public func roundCorner(radius: CGFloat,cornerColor: UIColor)  {
        
        base.layer.dx_roundCorner(radius: radius, cornerColor: cornerColor)
    }
    
    /// 设置一个普通圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    ///   - corners: 圆角位置
    public func roundCorner(radius: CGFloat,cornerColor: UIColor,corners: UIRectCorner) {
        base.layer.dx_roundCorner(radius: radius, cornerColor: cornerColor, corners: corners)
    }
    
    ///  设置一个带边框的圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    ///   - corners: 圆角位置
    ///   - borderColor: 边框颜色
    ///   - borderWidth: 边框线宽
    func roundCorner(radii: CGSize,cornerColor: UIColor, corners: UIRectCorner, borderColor: UIColor, borderWidth: CGFloat)  {
        base.layer.dx_roundCorner(cornerRadii: radii, cornerColor: cornerColor, corners: corners, borderColor: borderColor, borderWidth: borderWidth)
    }
}
```

### 使用示例

```swift
let circle = UIView()
circle.dx.roundCorner(radius: 15/2, cornerColor: color)

label.dx.roundCorner(radius: 20/2, cornerColor: UIColor.white,corners: [.topLeft,.topRight, .bottomRight, .bottomLeft])
```
### 简单解析一下
此处`.dx`语法拿到是`RoundCorner`这个泛型实例对象,而之后链式调用的`roundCorner(radius: CGFloat,cornerColor: UIColor)`方法,实际上是`RoundCorner`这个泛型实例对象进行的调用,这个泛型实例对象`RoundCorner`内部拥有这一个`base`就是遵守了`RoundCornerCompatible`这个协议的`UIView`

### 重新组织一下语言
绕了半天,`RoundCorner`这个就**相当于一个代理**,它内部拥有一个`base`就是方法调用者,例如`label`,而这个代理拥有某个功能,比如切圆角的功能,调用者需要遵守协议,那么你就可以使用我的功能

[demo下载示例](https://github.com/dongxiexidu/UIViewRoundCorner)

[参考文章:Kingfisher学习笔记](https://www.jianshu.com/p/470944c81e28)
