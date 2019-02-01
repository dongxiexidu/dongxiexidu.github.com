---
layout: post
title: protocol与delegate的区别
date: 2017-07-28
categories: Swift
tags: 代理/协议
---
>protocol协议:一个只有方法体(没有具体实现)的类，Java中称作接口，实现协议的类必须实现协议中

>delegate代理或委托:是一种设计模式。以协议的方式去体现(可以理解为协议的一种)，区别在于代码中常以<xxxDeletgate>标示，以便清晰辨明此为代理
### protocol示例1
```swift
protocol SortType {
    func sort(items: Array<Int>) -> Array<Int>
}

/// 冒泡排序：时间复杂度----O(n^2)
class BubbleSort: SortType {
    func sort(items: Array<Int>) -> Array<Int> {
        print("冒泡排序：")
    }
}
/// 插入排序-O(n^2)
class InsertSort: SortType {
    func sort(items: Array<Int>) -> Array<Int> {
        print("插入排序")
    }
}
```
### 外部调用
```Swift
func testSort(sortObject: SortType) {
    let list: Array<Int> = [2, 8, 9, 3, 5]
    let sortList = sortObject.sort(items: list)
    print(sortList)
}

testSort(sortObject: BubbleSort())
testSort(sortObject: InsertSort())
```
通过协议,我们看到这些对应的算法类具有统一的方法,这样做的好处显而易见,**代码相当的规范**

### protocol示例2
```swift
protocol Nibloadable {
    
}
// 自定义View,遵守此协议,便可以用此方法加载Nib文件
extension Nibloadable where Self : UIView{
    static func loadFromNib() -> Self {
        return Bundle.main.loadNibNamed("\(self)", owner: nil, options: nil)?.first as! Self
    }
}
```

外界调用
```
class AuthentiView: UIView,Nibloadable {
    // ...
}
// 需要AuthentiView遵守Nibloadable协议
let view = AuthentiView.loadFromNib()
```
只要这个类遵守某某协议,便拥有了某项能力,某个功能,或者说白了就拥有了调用协议的某个方法的能力,我们经常看到说Swift是面向协议开发的语言,核心点其实就是这样子

再次举个例子,比如说验证号码是否是有效手机号,这在实际项目开发中经常用到,不仅仅是登录页面,通常我们可以封装一个工具类,比如`Helper`工具类
```swift
class Helper: NSObject {
    // 检查是否是手机号
    static func checkMobileAction(mobile: String?)-> Bool{
        guard let mobile = mobile else { return false }
        if mobile.count != 11{
            return false
        }
        // ...
        return true
    }
}
// 调用
let result = Helper.checkMobileAction(mobile: 1501234567)
print(result)
```

使用协议的`extension`来实现这个功能
```Swift
protocol MobileProtocol {
    
}
extension MobileProtocol{
    // 检查是否是有效手机号
    func checkMobileAction(mobile: String?)-> Bool{
        guard let mobile = mobile else { return false }
        if mobile.count != 11{
            return false
        }
        // ...
        return true
    }
}
```
那么遵守这个协议,便拥有了某项能力,如上,检查输入是否为有效手机号
```Swift
class LoginController: MobileProtocol{
    func testProcotol(){
        // 检查输入是否为有效手机号
        let result = self.checkMobileAction(mobile: "1501234567")
        print(result)
    }
}
```
如果从调用者角度来看,好像方法是写在类内部一样`self.checkMobileAction(mobile: "1501234567")`,而实际上是在遵守的这个协议上

### protocol示例3
把网络请求的封装到对应的协议`extension`中
```Swift
protocol AuctionRequestProtocol {
    
}
extension AuctionRequestProtocol{
    // 封装请求失败
    func requestError(tableView: UITableView,error: String,target:UIViewController,action:Selector,frame: CGRect) {
        
       // ...
    }
}
```
具体的业务逻辑处理,UI的展示可以在此处理,以达到给controller减压的目的
```Swift
//MARK: 请求竞拍列表
extension AuctionRequestProtocol where Self: AuctionListController{

    internal func auctionListHttps(channel: String, tableView:UITableView,target:UIViewController,action:Selector, finishedCallBack : @escaping ([AuctionListModel]?) -> ()) {
        // ...
    }
}
//MARK: 请求竞拍详情
extension AuctionRequestProtocol where Self: AuctionDetailController{
    
    internal func auctionDetailHttps(channel: String, tableView:UITableView,target:UIViewController,action:Selector, finishedCallBack : @escaping ([AuctionListModel]?) -> ()) {
        // ...
    }
}
```
这里的扩展使用了`where`关键字进行了限制,指定对应对象才可以调用此方法.如果想要复用这个方法(不同的对象调用这个方法),可以把关键字`where`去掉

控制器中调用就代码非常简洁了,代码示例如下
```swift
extension AuctionListController: AuctionRequestProtocol{
    
    @objc func headerRefresh() {
        self.auctionListHttps(channel: type.rawValue, tableView: tableView, target: self, action: #selector(headerRefresh)) { (array) in
            
            self.tableView.reloadData()
        }
    }
}
```

### delegate应用示例
最经典的莫过于系统的`UITableView`了,不过`tableView`有点烂大街了.
```Swift
// 声明一个代理
protocol SliderViewDelegate:NSObjectProtocol {
    func sliderViewValueChange(leftValue: Int,rightValue: Int,sliderView: SliderView)
}

class SliderView: UIControl {
     open weak var delegate : SliderViewDelegate?
    
     private func updateSliderValue() {
        // ....
        if let delegate = delegate {
            delegate.sliderViewValueChange(leftValue: currentLeftValue, rightValue: currentRightValue, sliderView: self)
        }
    }
}    
```
**相同点**:代理和协议写法上基本一样,都是protocol进行声明,一般都是声明若干个(分可选,必须)方法,让遵守这个协议的对象(比如控制器)实现这些若干方法.

**区别在于**:代理可以作为专门处理特定事情的对象,在此处,我们在`SliderView`中声明了一个delegate对象,处理值变化的时候,我们可以把值,及时的传递给我们的对象,比如我们的控制器

简单总结:协议只是一个公共的接口，里面会规定你必须实现的一些东西.而代理是使用了协议,或者说在协议的基础上,扩充了功能,比如进行对象之间的传值,事件的传递

在Objective-C中,是不支持协议扩展的,所以Swift中协议功能相当强大,这也就是为啥子Swift是面向协议开发的语言

在Swift语言开发中,业务逻辑等自定义类型部分，则应优先考虑面向协议编程
