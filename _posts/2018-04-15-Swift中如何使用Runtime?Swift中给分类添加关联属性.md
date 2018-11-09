---
layout: post
title: Swift中如何使用Runtime?Swift中如何给分类添加关联属性
date: 2018-04-15
tags: Runtime
---

**应用示例,利用Runtime给所有UIViewController添加全屏返回功能**

### 一.入口,重写`UIApplication`的`next`方法
```Swift
extension UIApplication {

    private static let runOnce: Void = {
        
        NothingToSeeHere.harmlessFunction()
    }()
    
    // 在applicationDidFinishLaunching方法之前调用
    override open var next: UIResponder? {
       
        UIApplication.runOnce
        return super.next
    }
}
```
定义一个类,定义一个静态方法,在这个方法中拿到所有遵守协议的类,进行方法的交换
```Swift
class NothingToSeeHere {
    static func harmlessFunction() {

        // 打印 11930 获取所有的类数量
        let typeCount = Int(objc_getClassList(nil, 0))
        
        // 在Swift中无类型的指针，原始内存可以用UnsafeRawPointer 和UnsafeMutableRawPointer来表示
        // 定义一个存放类的数组,capacity指定分配内存大小
        // 不提供自动内存管理，没有类型安全性
        let types = UnsafeMutablePointer<AnyClass>.allocate(capacity: typeCount)
        let autoreleasingTypes = AutoreleasingUnsafeMutablePointer<AnyClass>(types)
        // 获取所有的类,存放到数组types
        objc_getClassList(autoreleasingTypes, Int32(typeCount))
        
        // 如果该类实现了SelfAware协议，那么调用awake方法
        for index in 0 ..< typeCount {
            (types[index] as? SelfAware.Type)?.awake()
        }
        //        types.deallocate(capacity: typeCount)
        // 释放
        types.deallocate()
    }
}
```

```Swift
// MARK:- SelfAware 定义协议，使得程序在初始化的时候，将遵循该协议的类做了方法交换
protocol SelfAware: class {
    static func awake()
}
```

遵守协议,实现协议方法
```Swift
// MARK:UIViewController  交换viewWillAppear(_:)与viewWillDisappear(_:)方法
extension UIViewController:SelfAware {
    static func awake() {
        UIViewController.classInit()
        UINavigationController.classInitial()
    }
    
    static func classInit() {
        swizzleMethod
    }
    private static let swizzleMethod: Void = {
        let originalSelector = #selector(viewWillAppear(_:))
        let swizzledSelector = #selector(swizzled_viewWillAppear(_:))
        swizzlingForClass(UIViewController.self, originalSelector: originalSelector, swizzledSelector: swizzledSelector)
        
        let originalSelector1 = #selector(viewWillDisappear(_:))
        let swizzledSelector1 = #selector(swizzled_viewWillDisAppear(_:))
        swizzlingForClass(UIViewController.self, originalSelector: originalSelector1, swizzledSelector: swizzledSelector1)
    }()
		
	…
}
```

导航控制器
```Swift
// MARK:UINavigationController  交换pushViewController(_:animated:)方法
extension UINavigationController {
    static func classInitial() {
        swizzleMethod
    }
    
    private static let swizzleMethod: Void = {
        let originalSelector = #selector(UINavigationController.pushViewController(_:animated:))
        let swizzledSelector = #selector(dx_pushViewController)
        swizzlingForClass(UINavigationController.self, originalSelector: originalSelector, swizzledSelector: swizzledSelector)
    }()
	…
}
```

### 二.如何在Swift中给分类添加关联属性
```Swift
extension  UIViewController {
    
    // MARK:- RuntimeKey   动态绑属性
    struct RuntimeKey {
        
        // 在Swift中无类型的指针，原始内存可以用UnsafeRawPointer 和UnsafeMutableRawPointer来表示
        // A raw pointer for accessing untyped data 用于访问非类型数据的原始指针
        // init(bitPattern:) 从指定地址创建一个新的原始指针，指定为位模式
        
        // 哈希: http://swifter.tips/hash/
        // 比如 Int 的 hashValue 就是它本身：
        // print("dx_popDisabled".hashValue) 402467026446327185
        static let dx_popDisabled = UnsafeRawPointer.init(bitPattern: "dx_popDisabled".hashValue)
        
        static let dx_navigationBarHidden = UnsafeRawPointer.init(bitPattern: "dx_navigationBarHidden".hashValue)

    }
    
    // MARK:- 是否开启侧滑，默认true
    public var dx_popDisabled: Bool? {
        set {
            objc_setAssociatedObject(self, RuntimeKey.dx_popDisabled!, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
        get {
            return objc_getAssociatedObject(self, RuntimeKey.dx_popDisabled!) as? Bool
        }
    }

```
