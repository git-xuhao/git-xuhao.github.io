---
layout: post
title: Android 性能检测工具
permalink: performance_tests
date: 2016-06-27 14:34:00
categories: Android
tags: Android 性能优化

---

#### Android常见问题
- 内存泄漏：不使用的内存空间(对象)一直被引用着没有得到释放。

- 内存抖动：短时间内大量创建对象又在短时间内频繁触发GC导致内存波动很大，android虚拟机执行GC操作时需要耗费CPU性能频繁GC会到来严重的性能问题。内存抖动是因为大量的对象被创建又在短时间内马上被释放。瞬间产生大量的对象会严重占用Young Generation的内存区域，当达到阀值，剩余空间不够的时候，也会触发GC。即使每次分配的对象占用了很少的内存，但是他们叠加在一起会增加Heap的压力，从而触发更多其他类型的GC。这个操作有可能会影响到帧率，并使得用户感知到性能问题。

- 内存溢出：内存泄漏到一定程度超出系统给进程分配的内存大小就会造成内存溢出程序奔溃，或者加载一个或多个大型文件(图片)到内存中也会导致内存溢出。

- ANR：应用无响应，代码效率过低或者在主线程执行耗时操作。

<!-- more -->

#### 内存监控工具
- [adb shell dumpsys meminfo](http://zmywly8866.github.io/2016/05/04/android-application-leak-analysis-and-fix.html)：内存泄露检测和改善。

- [Memory Monitor](http://www.kancloud.cn/digest/itfootballprefermanc/100906)：查看整个app所占用的内存，以及发生GC的时刻，短时间内发生大量的GC操作是一个危险的信号。

- [Allocation Tracker](http://www.kancloud.cn/digest/itfootballprefermanc/100909)：使用此工具来追踪内存的分配。

- [Heap Viewer](http://www.kancloud.cn/digest/itfootballprefermanc/100907)：实时查看App分配的内存大小和空闲内存大小，发现Memory Leaks

- [Heap Snapshot](http://www.kancloud.cn/digest/itfootballprefermanc/100910)：获取Java堆内存详细信息，可以分析出内存泄漏的问题

- [Memory Analyzer Tool](http://www.kancloud.cn/digest/itfootballprefermanc/100912)：MAT一款详细分析Java堆内存的工具，该工具非常强大，为了使用该工具，我们需要hprof文件，该文件我们在之前的Heap Snapshot工具的时候，我们就生成了该文件。但是该文件不能直接被MAT使用，需要进行一步转化，可以使用hprof-conv命令来转化，但是Android Studio可以直接转化。

#### 性能监控工具
- [Profile GPU Rendering](http://blog.csdn.net/xu_fu/article/details/45008779)：连续多帧每一帧的刷新时间都超过16ms就应该被视为性能问题。

- [Profile GPU Rendering : M Update](http://hukai.me/android-performance-patterns-season-5/)：Profile GPU Rendering的升级版。

- [TraceView](http://www.kancloud.cn/digest/itfootballprefermanc/100911)：从代码层面分析性能问题，针对每个方法来分析，比如当我们发现我们的应用出现卡顿的时候，我们可以来分析出现卡顿时在方法的调用上有没有很耗时的操作，关注以下两个问题：
+ 调用次数不多，但是每一次执行都很耗时
+ 方法耗时不大，但是调用次数太多

- [Systrace](http://www.kancloud.cn/digest/itfootballprefermanc/100913)：计算容器的性能并发现性能的瓶颈。

- [GPU Monitor](http://www.kancloud.cn/digest/itfootballprefermanc/100914)：分析GPU的性能，实时查看绘制每一帧所花费的时间，与Profile GPU Rendering差不多。

- [Strict Mode](http://tech.it168.com/a2011/0908/1243/000001243936_all.shtml)：UI线程被阻塞超过5秒，就会出现ANR，这太糟糕了。防止程序出现ANR是很重要的事情，那么如何找出程序里面潜在的坑，预防ANR呢？很多大部分情况下执行很快的方法，但是他们有可能存在巨大的隐患，这些隐患的爆发就很容易导致ANR。
Android提供了一个叫做Strict Mode的工具，我们可以通过手机设置里面的开发者选项，打开Strict Mode选项，如果程序存在潜在的隐患，屏幕就会闪现红色。我们也可以通过StrictMode API在代码层面做细化的跟踪，可以设置StrictMode监听那些潜在问题，出现问题时如何提醒开发者，可以对屏幕闪红色，也可以输出错误日志。

- [LINT](http://blog.csdn.net/xyz_lmn/article/details/14222939)：Lint是Android提供的一个静态扫描应用源码并找出其中的潜在问题的一个强大的工具。
例如，如果我们在onDraw方法里面执行了new对象的操作，Lint就会提示我们这里有性能问题，并提出对应的建议方案。Lint已经集成到Android Studio中了，我们可以手动去触发这个工具，点击工具栏的Analysis -> Inspect Code，触发之后，Lint会开始工作，并把结果输出到底部的工具栏，我们可以逐个查看原因并根据指示做相应的优化修改。
Lint的功能非常强大，他能够扫描各种问题。当然我们可以通过Android Studio设置找到Lint，对Lint做一些定制化扫描的设置，可以选择忽略掉那些不想Lint去扫描的选项，我们还可以针对部分扫描内容修改它的提示优先级。
建议把与内存有关的选项中的严重程度标记为红色的Error，对于Layout的性能问题标记为黄色Warning。

- [Monkey](http://www.cnblogs.com/vanezkw/archive/2013/05/24/3096815.html)：Monkey是可以运行在模拟器里或实际设备中的程序。它向系统发送伪随机的用户事件流(如按键输入、触摸屏输入、手势输入等)，实现对正在开发的应用程序进行压力测试。

- [其他工具](http://zmywly8866.github.io/2015/09/09/android-performance-tools.html)：其他性能检测工具

#### 网络监控工具
- [Network monitor](http://www.kancloud.cn/digest/itfootballprefermanc/100915)：实时监控上传和接收的网速