---
layout: post
title: iOS之CPU和GPU
date: 2015-02-04
tags: 性能
---


## 在屏幕成像的过程中,CPU和GPU起着至关重要的作用

### CPU: Central Processing Unit 中央处理器
对象的创建和销毁,对象属性的调整,布局计算,文本的计算和排版,图片的格式转换和解码,图像的绘制Core Graphics

### GPU: Graphics Processing Unit, 图形处理器
纹理的渲染


### CPU --计算--> GPU --渲染--> 帧缓存 --读取--> 视屏控制器 --显示--> 屏幕

**在iOS中是双缓冲机制,有前帧缓存,后帧缓存**



### 卡顿产生的原因
CPU计算和GPU渲染如果时间过长,那么垂直信号(`VSync`)渲染到屏幕上的图形仍然是上一次的界面.

卡顿解决的主要思路:
尽可能减少CPU,GPU资源消耗

按照60FPS的刷帧率,每隔16ms就会有一次`VSync`信号

### 针对CPU的优化
- 尽量用轻量级的对象,比如用不到事件处理的地方,可以考虑使用`CALayer`取代`UIView`
- 不要频繁地调用UIView的相关属性,比如frame,bounds,transform等属性,尽量减少不必要的修改
- 尽量提前计算好布局,在有需要的时候,一次性调整对应的属性,避免多次修改属性
- Autolayout会比直接设置frame消耗更多的CPU资源
- 图片的size最好刚好跟UIImageView的size保持一致
- 控制线程的最大并发数量
- 尽量把耗时的操作放到子线程,比如,文本处理(尺寸计算,绘制),图片处理(解码,绘制)

### 针对GPU的优化
- 尽量减少视图数量和层次
- 尽量避免短时间内大量图片的显示,尽肯能将多张图片合成一张进行显示
- GPU能处理的最大纹理尺寸是4096*4096,一旦超过这个尺寸,就会占用CPU资源进行处理,所以纹理尽量不要超过这个尺寸
- 减少透明的视图(alpha < 1),不透明的就设置opaque为YES,一旦有透明度,就会增加CPU的工作,就会去计算这个色值如何显示
- 尽量避免出现离屏渲染

## 离屏渲染
### 在OpenGL中,GPU有2种渲染方式
- 1.On-Screen Rendering: 当前屏幕渲染,在当前用于显示的屏幕缓冲区进行渲染操作
- 2.Off-Screen Rendering: 离屏渲染,在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作


### 离屏渲染消耗性能的原因
- 需要创建新的缓冲区
- 离屏渲染的整个过程中,需要多次切换上下文环境,先是从当前屏幕(On-Screen)切换到离屏(Off-Screen);等到离屏渲染结束以后,将离屏缓冲区的渲染结果显示到屏幕上,有需要将上下文环境从离屏切换到当前屏幕

### 哪些操作会触发离屏渲染?
- 光栅化,layer.shouldRasterize = YES
- 遮罩,layer.mask
- 圆角,同时设置layer.masksToBounds = YES, layer.cornerRadius大于0
考虑通过CoreGraphics绘制裁剪圆角,或者叫设计师提供圆角图片

- 阴影,layer.shadow
如果设置了layer.shadowPath就不会产生离屏渲染

## 卡顿检测
>平时所说的卡顿,主要是在主线程执行了比较耗时的操作

>可以添加Observer到主线程RunLoop中,通过监听RunLoop状态切换的耗时,以达到监控卡顿的目的;

示例项目 
[LXDAppFluecyMonitor](https://github.com/UIControl/LXDAppFluecyMonitor)
