---
layout: post
title: C++之模板(template)
date: 2012-03-06
tags: C++
---

## 泛型,是一种将类型参数化以达到代码复用的技术,C++中使用模板来实现泛型

### 模板没有被使用时,是不会被实例化出来的

### 模板的声明和实现如果分离到.h和.cpp中,会导致链接错误
.h文件不参与编译,它的作用就是替换.编译的时候会通过,但是链接的时候,会真正的去找函数的地址,找不到

### 模板的声明和实现统一放到一个.hpp文件中
使用的时候,只需要导入.hpp文件
```swift
#include "Swap.hpp"
```

代码示例:
```swift
void swapValues(int &v1, int &v2) {
    int tmp = v1;
    v1 = v2
    v2 = tmp;
}
// 函数重载
void swapValues(double &v1, double &v2) {
    int tmp = v1;
    v1 = v2
    v2 = tmp;
}
```
泛型
```swift
// 将类型参数化 class == typename
template <class T> void swapValues(T &v1, T &v2) {
    T tmp = v1;
    v1 = v2
    v2 = tmp;
}
```

仿函数使用示例
```swift
double a = 10.4;
double b = 20.5;
swapValues(a,b);
cout << "a = " << a << "b =" << b << endl; 
```

泛型函数使用示例
```swift
double a = 10.4;
double b = 20.5;
// 将类型参数化,类型一般也传过去
swapValues<double>(a,b);
cout << "a = " << a << "b =" << b << endl; 

int c = 10;
int d = 20;
swapValues<int>(a,b);
```
通过断点,反汇编查看,函数调用地址不一样,说明泛型原理是编译器生成了两个函数
```swift
call swapValues<int>    (0DB1429h)
call swapValues<double> (0DB141Fh)
```

## 多参数模板
```swift
template <class T1, class T2>
void display(const T1 &v1, const T2 &v2){
    cout << v1 << endl;
    cout << v2 << endl;
}
// 调用示例
display(20,1.5)
```
