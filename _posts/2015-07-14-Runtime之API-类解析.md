---
layout: post
title: Runtime之API-类解析
date: 2015-07-14
tags: Runtime
---

### 常用API简介
```swift
// 动态创建一个类
objc_allocateClassPair(Class superclass, const char *name, size_t extraBytes)
// 销毁一个类
objc_disposeClassPair(Class cls)
// 获取isa指向的class
objc_getClass(const char * name)
// 判断一个对象是否是class
object_isClass(id obj)
// 判断一个类是否是一个元类
class_isMetaClass(Class cls)
// 获取父类
class_getSuperclass(Class cls)
// 设置isa指向的class()
object_setClass(id obj, Class cls)
```

示例
```swift
// 创建类
Class newClass = objc_allocateClassPair([NSObject class], "DXDog", 0);
// 添加成员变量,注意:添加成员必须要在注册类之前
class_addIvar(newClass, "_age", 4, 1, @encode(int));
class_addIvar(newClass, "_weight", 4, 1, @encode(int));
// 添加方法,可以在注册之后再次添加方法
class_addMethod(newClass, @selector(run), (IMP)run, "v@:");
// 注册类
objc_registerClassPair(newClass);

void run(id self, SEL _cmd){
    NSLog(@"_____ %@ - %@", self, NSStringFromSelector(_cmd));
}
```


### 使用动态创建的类
```swift
id dog = [[newClass alloc] init];
// 可以使用KVC进行赋值操作
[dog setValue:@10 forKey:@"_age"];
[dog setValue:@20 forKey:@"_weight"];
[dog run];
NSLog(@"%@ %@", [dog valueForKey:@"_age"], [dog valueForKey:@"_weight"]);

// 在不需要这个类时释放
objc_disposeClassPair(newClass);
```

示例
```swift
DXPerson *person = [[DXPerson alloc] init];
// [DXPerson run]
[person run];

// 修改对象isa指向后,变成了DXCar类型,执行DXCar对象的run方法
object_setClass(person, [DXCar class]);
// [DXCar run]
[person run];

// 0 1 1
NSLog(@"%d %d %d",
object_isClass(person),
object_isClass([DXPerson class]),
object_isClass(object_getClass([DXPerson class]))
);
```
