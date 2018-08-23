---
layout: post
title: Android 异常退出的生命周期分析
permalink: Android-exception-exit
date: 2016-04-25 19:02:33
categories: Android
tags: Android
desc: 典型的Activity 生命周期众所周知，就不用多说了吧；我们知道，Activity除了受用户操作所导致的正常的生命周期方法调度，还有一些异常情况，比如当资源相关的系统配置发生了改变以及系统内存不足时，Activity就可能被杀死，下面就来具体分析这两种情况。
---


**概述：**
---

典型的Activity 生命周期众所周知，就不用多说了吧；我们知道，Activity除了受用户操作所导致的正常的生命周期方法调度，还有一些异常情况，比如当资源相关的系统配置发生了改变以及系统内存不足时，Activity就可能被杀死，下面就来具体分析这两种情况。
<!-- more -->
#### **情况一 ：** 当一个activity的config改变的时候，Activity就会被杀死然后重建
而我们最熟悉的config的改变就是屏幕方向改变，系统语言改变，还有键盘可见改变了等等，系统配置发生了改变，在默认情况下，Activity就会被销毁并且重新创建，当然我们也可阻止系统重新创建我们的Activity。

在默认情况下，如果我们的Activity不做特殊处理，那么当系统配置发生改变后，Activity就会被销毁并重新创建，如图所示：
![这里写图片描述](htts://img-blog.csdn.net/20160425154234928)

当系统配置发生改变后，Activiy会被销毁，其onPause、onStop、onDestroy 均会被调用，同时由于Activiy 是在异常情况下终止的，系统会在onStop之前调用onSaveInstanState来保存当前Activiy的状态。
所以当出现异常有重要的数据需要恢复的，要在这里做一些数据的保存工作；系统只会帮我们保存视图。

```
@Override
protected void onSaveInstanceState(Bundle outState) { 
    super.onSaveInstanceState(outState); 
   //在这里我们可以把我们需要保存的东西给写入Bundle 
   //....... 
}
```
然后我们保存的数据就可以在onCreate中来读取了：

```
@Override protected void onCreate(Bundle savedInstanceState) {       
    super.onCreate(savedInstanceState); 
    setContentView(R.layout.activity_main);
    if(savedInstanceState != null){
        //TODO 取出数据
    } 
  }
```
另一种就是一个独立的回调了：

```
@Override
protected void onRestoreInstanceState(Bundle savedInstanceState) { 
    super.onRestoreInstanceState(savedInstanceState); 
}
```
二者的区别是：onRestoreInstanceState一旦被调用，其参数Bundle savedInstanceState 一定是有值的，我们不用额外的判断是否为空；但是onCreate不行，这两个方法都可以进行数据恢复，但是Google建议采用onRestoreInstanceState 去恢复数据。
  <br/>
#### **情况二：** 资源内存不足导致低优先级的Activity被杀死
 
Activity 按照优先级从高到低，可以分为三种：
（1）前台Activity——正在和用户交互的Activity，优先级最高。
（2）可见但非前台Activity——比如Activity中弹出了一个对话框，导致Activity可见但是位于后台无法和用户直接交互。
（3）后台Activity——已经被暂停的Activity，比如执行了onStop，优先级最低。


当系统内存不足时，系统就会按照上述优先级去杀死目标Activity所在的进程，并通过onSaveInstanState和onRestoreInstanceState 来存储和恢复数据。如果一个进程中没有四大组件在执行，那么这个进程将很快被系统杀死，因此，一些后台工作不适合脱离四大组件而独自运行在后台中，这样进程很容易被杀死。建议将后台工作放入Service中从而保证进程有一定的优先级，这样就不会轻易地被系统杀死了。
  
**NOTES**：虽然内容很简单，但也是博主辛苦的做了下笔记，转载请注明出处！


