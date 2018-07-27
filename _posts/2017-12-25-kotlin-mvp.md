---
layout: article
title: 基于Kotlin+Mvp+RxJava+Retrofit架构开发的短视频APP
key: 100011
tags: kotlin mvp
category: blog
date: 2017-12-25 01:15:00 +08:00
---

# KotlinMvp

[![](https://img.shields.io/badge/QQ-504105930-blue.svg)](http://xuhaoblog.com)

![这里写图片描述](http://oyp2zrwnm.bkt.clouddn.com/ic_launcher.png)

GitHub地址：[https://github.com/git-xuhao/KotlinMvp](https://github.com/git-xuhao/KotlinMvp)

（开源不易，如果喜欢的话希望给个 Star 或者 Fork，谢谢！）

《KotlinMvp》 是仿着“开眼Eyepetizer”之前版本并加上自己的想法，开发的一款的短视频小应用，每日为你推荐精选视频，让你大开眼界。本项目采用 Kotlin 语言编写，结合 MVP+RxJava2+Retrofit2+Glide等的架构设计，学习 Kotlin 利用空余时间开发的一款小项目，代码结构清晰有详细注释，如有任何疑问和建议请提 Issue或联系[![](https://img.shields.io/badge/Gmail:-igeekho@gmail.com-blue.svg)]()
<!--more-->
## 项目截图

![](http://oyp2zrwnm.bkt.clouddn.com/pt2017_12_09_10_27_10.jpg)


- gif

   <img src="http://oyp2zrwnm.bkt.clouddn.com/kotlin-mvp-1.gif">
## 下载体验

点击[![](https://img.shields.io/badge/Download-apk-green.svg)](https://fir.im/kotlinmvp) 或者扫描下方二维码

<img src="http://oyp2zrwnm.bkt.clouddn.com/QR-code-kotlin-mvp.png"/>



## 技术要点
主要使用的第三方开源框架有：

 - [RxJava](https://github.com/ReactiveX/RxJava)
 - [RxAndroid](https://github.com/ReactiveX/RxAndroid)
 - [Retrofit](https://github.com/square/retrofit)
 - [Glide](https://github.com/bumptech/glide)
 - [Logger](https://github.com/orhanobut/logger)
 - [FlycoTabLayout](https://github.com/H07000223/FlycoTabLayout)
 - [Flexbox-layout](https://github.com/google/flexbox-layout)
 - [RealtimeBlurView](https://github.com/mmin18/RealtimeBlurView)
 - [SmartRefreshLayout](https://github.com/scwang90/SmartRefreshLayout)
 - [BGABanner-Android](https://github.com/bingoogolapple/BGABanner-Android)
 - [GSYVideoPlayer](https://github.com/CarGuo/GSYVideoPlayer)

模块说明：

 - **每日精选：**首页采用下拉刷新+RecyclerView 实现，Banner展示每日精选推荐的视频，监听 RecyclerView 的滑动事件，实现 TitleBar 的隐藏显示功能，底部菜单采用高斯模糊的半透明效果，使整个 APP 风格更加清爽。
 - **发现：** 包括关注和分类模块，关注是推荐的作者上传的视频，分类包括时尚、运动、创意、广告、音乐、旅行、生活、记录、开胃、游戏、萌宠、动画、综艺、搞笑等可自由选择想查看的类型视频。
 - **热门：** 热门排行榜包括周排行、月排行、总排行的视频列表。
 - **搜索：** 根据关键字搜索榜你找到感兴趣的视频。
 - **我的：** 个人主页的相关介绍。

## 更新日志

**v1.0**

 - 初始化版本，主要功能已经完成。

## 关于我

 - Email: igeekho@gmail.com
 - Home: [https://xuhaoblog.com](https://xuhaoblog.com)
 - CSDN:[http://blog.csdn.net/u011974987](http://blog.csdn.net/u011974987)
 - 简书: [http://www.jianshu.com/u/25bc3e1e7595](http://www.jianshu.com/u/25bc3e1e7595)

## Thanks

- 感谢所有优秀的开源项目
- [Eyepetizer](https://github.com/kaikaixue/Eyepetizer) ：最初是看了这个项目才开始做的，学了不少东西，写出了我自己的代码风格

## 声明
**项目中的 API 均来自开眼视频，纯属学习交流使用，不得用于商业用途！**

## LICENSE
```
Copyright 2017 Xiho

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

