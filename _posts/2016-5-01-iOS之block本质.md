---
layout: post
title: iOS之block本质
date: 2016-05-01
tags: 
---

### block本质上也是一个`Objective-C`对象,它内部也有个isa指针
如何验证? 书写一个含有参数的block,并且使用了外部变量,使用clang编译器,终端转成C++文件,查看底层实现
```swift
int a = 1;
int b = 2;
int age = 10;
void(^myBlock)(int, int) = ^(int a, int b) {
    NSLog(@"a = %d, b = %d,age = %d", a, b,age);
};

myBlock(a,b);
```

转成C++代码
```swift
int a = 1;
int b = 2;
int age = 10;

// 定义block变量
void(*myBlock)(int, int) = ((void (*)(int, int))&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, age));

// 执行block内部代码
((void (*)(__block_impl *, int, int))((__block_impl *)myBlock)->FuncPtr)((__block_impl *)myBlock, a, b);
```

看起来有点复杂,删掉一些强制转换的代码,这样看起来就清爽了
```swift
void(*myBlock)(int, int) = &__main_block_impl_0(__main_block_func_0, &__main_block_desc_0_DATA, age);

myBlock->FuncPtr(myBlock, a, b);
```
为什么`myBlock`可以直接指向`FuncPtr`?是由于强制转换成了`(__block_impl *)`

**为什么可以强制转换?**因为`__main_block_impl_0`的第一个成员变量的地址就是这个`__main_block_impl_0`的地址




查看`__main_block_impl_0`结构体
```swift
struct __main_block_impl_0 {

  // 第一个成员变量的地址就是这个`__main_block_impl_0`的地址
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  // 外部变量会封装到block对象里面
  int age;
  
  // C++构造函数,返回结构体对象 flags可选参数,
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```

结构体变量
```swift
static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} 

// 结构体变量  0 赋值给了reserved   sizeof(struct __main_block_impl_0)赋值给了Block_size
__main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};
```

查看`__block_impl`结构体
```swift
struct __block_impl {
  void *isa;
  int Flags;
  int Reserved;
  void *FuncPtr;
};
```
**isa指向类对象**


- block是封装了函数调用以及函数调用环境的`Objective-C`对象

block的调用,就是封装的函数的调用,如下
```swift
static void __main_block_func_0(struct __main_block_impl_0 *__cself, int a, int b) {

    int age = __cself->age; // bound by copy
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_q8_6tzlv22s7xx_m6qplb1fbq7m0000gn_T_main_05dd94_mi_0, a, b,age);
}
```

函数的地址存在 : 结构体`__main_block_func_0`-> 结构体`struct __block_impl`的 `impl`的`FuncPtr`

查看`__main_block_desc_0`指针`* Desc`
```swift
static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} 
```
变量`Block_size`这个block占用多少内存


### 总结block里面,封装了
- 1.isa指针
- 2.函数地址FuncPtr
- 3.block占用多少内存
- 4.使用到的环境变量age

- block底层结构

## block面试题

### 1.如下打印是多少???
```swift
int age = 10;
void(^myBlock)(void) = ^ {
    NSLog(@"age = %d",age);
};
age = 20;
myBlock();
```

由C++构造函数可知道
```swift
  // C++构造函数,返回结构体对象 flags可选参数,
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
  ```
  _age会赋值给成员变量age = 10;因此是10,变量值已经捕获(capture),所谓捕获,就是结构体专门新增一个变量存储这个值;
  
  
  **为了保证block内部能够正常访问外部的变量,block有个变量捕获机制**
  
  
### 2.如下打印是多少???
```swift
static int age = 10;
void(^myBlock)(void) = ^ {
    NSLog(@"age = %d",age);
};
age = 20;
myBlock();
```

转换成C++代码后,结构体的age成员变量变成了`*age;`
```swift
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  int *age;
  // 构造函数传参也是*_age
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int *_age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```
block调用,也是`*age`
```swift
NSLog((NSString *)&__NSConstantStringImpl__var_folders_q8_6tzlv22s7xx_m6qplb1fbq7m0000gn_T_main_97ee31_mi_0, a, b,(*age));
```

可以看到构造函数传参也是`*_age`,因为是地址传递,所以打印是20
  
### 3.为什么auto变量是值传递,而不是指针传递?
```swift
void (^myBlock)(void);
void test{
    // 自动变量: 默认局部变量,都是有auto 离开作用域就会销毁
    autu int age = 10;
    myBlock = ^{
        NSLog(@"age = %d", age);
    }
    age = 20;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        test();
        myBlock();
    }
    return 0;
}
```

如上代码,自动变量age在大括号后就会销毁,而block之后才执行,假如是地址传递,那么*age就是野指针了


### 3.如果是全局变量,block会捕获变量吗?如下打印是多少?
```swift
// 全局变量
int b = 2;
static int age = 10;

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        auto int a = 1;
        void(^myBlock)(int) = ^(int a) {
            NSLog(@"a = %d, b = %d, age = %d", a, b,age);
        };
        a = 5;
        b = 6;
        age = 7;
        myBlock(a);
    }
    return 0;
}
```

转换成C++代码后,可以看到在C++代码中,也是全局变量,而且并没有捕获变量,构造函数也没对应的参数
```swift
int b = 2;
static int age = 10;

struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  // 构造函数
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int flags=0) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```
因为是全局变量,所以不管哪里都可以访问到,因此就没有必要捕获这个变量,不管有没有加`static`




![demo]({{ "/assets/img/capture.png" | absolute_url }})
