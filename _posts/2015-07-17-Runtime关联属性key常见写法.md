---
layout: post
title: Runtime关联属性key常见写法
date: 2015-07-17
tags: Runtime
---

## 常见面试题回顾:
### 1.能否给Category直接添加成员变量?
答:不可以,编译器会报错`Instance variables may not be placed in Categories`,如图
![error.jpg](https://upload-images.jianshu.io/upload_images/987457-7eac9d9679d24602.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.Category是否可以添加属性?
答:可以.但是
- 1.只会生成setter和getter方法的声明
- 2.不会生成下划线成员变量
- 3.也不会生成setter和getter方法的实现

因此如果给Category仅仅是添加了属性,而没有手动实现setter和getter方法,那么这个属性是不能保存值的,就是说在外界是无法使用这个属性的.

### 3.如何给Category添加属性?
- 1.给Category添加属性
- 2.手动实现属性的setter和getter方法

### 4.如何实现Category的添加属性的setter和getter方法?
使用运行时的`objc_setAssociatedObject`和`objc_getAssociated`方法



# 关联属性key常见写法
```swift
// 相当于 NULL,
1.static const char DXNameKey;

// 利用静态变量地址唯一不变的特性
2.static const void *DXNameKey = &DXNameKey;

3.static NSString *DXNameKey = @"name"; 

// _cmd == @selector(当前方法名称)
4._cmd和@selector(_cmd对应的方法名称)
```
### 1.static const char DXNameKey
如果Category仅仅添加一个属性没有问题,如果添加多个属性,就会有问题
```swift
static const char  DXNameKey;
static const char  DXWeightKey;
```
因为
```swift
NULL ==  static const char DXNameKey
```
所以这种写法不推荐
### 2.利用静态变量地址唯一不变的特性
static const void *DXNameKey = &DXNameKey;
static const void *DXWeightKey = &DXWeightKey;
&DXNameKey就是`DXNameKey`这个静态变量的地址,而且是唯一的,因此也可以当做key
```swift
- (void)setName:(NSString *)name{
    objc_setAssociatedObject(self, DXNameKey, name, OBJC_ASSOCIATION_COPY_NONATOMIC);
}

- (NSString *)name{
    return objc_getAssociatedObject(self, DXNameKey);
}
```
### 3.使用字符串常量地址
```swift
objc_getAssociatedObject(id  _Nonnull object, const void * _Nonnull key)
```
既然是`const void *`,那么字符串字符串地址也是可以的
```swift
#define DXNameKey @"name"
// 或者
static NSString *DXNameKey = @"name"; 
```
```swift
- (void)setName:(NSString *)name{
    objc_setAssociatedObject(self, DXNameKey, name, OBJC_ASSOCIATION_COPY_NONATOMIC);
}

- (NSString *)name{
    return objc_getAssociatedObject(self, DXNameKey);
}
```

### 4.使用方法的隐式参数`_cmd`和`@selector(_cmd对应的方法名称)`
```swift
- (void)setName:(NSString *)name{
    objc_setAssociatedObject(self, @selector(name), name, OBJC_ASSOCIATION_COPY_NONATOMIC);
}

- (NSString *)name{
// 隐式参数
// _cmd == @selector(name)
    return objc_getAssociatedObject(self, _cmd);
}
```

`_cmd`类型是`SEL`,所以每个方法默认都有这个隐式参数,`_cmd`是当前方法的一个SEL指针,所以也可以当做key来使用

**需要注意,两个setter和getter方法,只能一个使用`_cmd`,另外一个要使用`@selector(_cmd对应的方法名称)`**

# 总结
使用`_cmd`不需要额外再声明一个key,省去声明一个key的内存开销,虽然这个开销可以忽略,写法也方便,因此推荐使用`_cmd`作为运行时关联属性key

| policy：关联策略 | 相当于 | 
| ---- | ---- |
|OBJC_ASSOCIATION_ASSIGN | @property(assign, nonatomic) |
|OBJC_ASSOCIATION_RETAIN_NONATOMIC |@property(strong, nonatomic) |
|OBJC_ASSOCIATION_COPY_NONATOMIC |@property(copy, nonatomic) |
|OBJC_ASSOCIATION_RETAIN |@property(strong,atomic)|
|OBJC_ASSOCIATION_COPY |@property(copy, atomic) |
