---
layout: post
title: Ubuntu 15.10 下载Android5.1代码环境搭建
key: 201604101
tags: Ubuntu Android
category: 技术
date: 2016-04-10 10:05:36 +08:00
desc: 作为一个Android开发者，想要自己提升一个阶段，想深入了解 Android 相关的知识，Activity 是工作流程是怎样的？ View 是怎样绘制的？ 怎样修改一些系统相关的应用？等等，就得要深入Android 源代码~
---



**概述：**
-------
作为一个Android开发者，想要自己提升一个阶段，想深入了解 Android 相关的知识，Activity 是工作流程是怎样的？ View 是怎样绘制的？ 怎样修改一些系统相关的应用？等等，就得要深入Android 源代码~所以就要先获取Android源码：

### 准备工作：

   1. 装一个Ubuntu系统（http://www.ubuntu.com/download/desktop）电脑的内存最好有 8 G，因为编译 Android 源码很吃电脑内存，特别是在 4.4 以后的版本，电脑内存大点，比较保险，编译一次源码要好几个小时，要因为内存原因，太让人郁闷了。
   ps:( ubuntu装完后把系统软件全都更新一下)
   2. 一个翻墙vpn（https://www.trueyt.com/）
   
 <!-- more -->

### 编译环境的部署：

   访问： http://source.android.com/source/initializing.html


**1、 Installing the JDK**
-------------------------

Android 5.0 编译需要 Open Java7 版本
```
$ sudo apt-get update
$ sudo apt-get install openjdk-7-jdk
```
Android 4.4 编译需要 Sun Java6 版本

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java6-set-default
```
切换 Java 的方法 (上面 2 个根据你的需要安装)

```
$ sudo update-alternatives --config java
$ sudo update-alternatives --config javac
```

**2、   Installing required packages**
-------------------------

```
$ sudo apt-get install bison g++-multilib git gperf libxml2-utils make python-networkx zlib1g-dev:i386 zip
$ sudo apt-get install git gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386
$ sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so
```

** Notes！！不同的 Ubuntu 版本可能不能，安装这些编译工具可能不成功，也不用管，在你编译的时候，可能会出错，根据错误日志去解决就行了。**

配置 USB 访问 (下面的都可能需要翻墙了)

```
$ wget -S -O - http://source.android.com/source/51-android.rules | sed "s/<username>/$USER/" |sudo tee >/dev/null /etc/udev/rules.d/51-android.rules; 
$ sudo udevadm control --reload-rules
```

安装 repo ， repo 是 git 的管理工具

```
$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```
### 下载源代码
（我这边添加的是内置服务器，从我们自己的服务器下载的代码很快，一般一个小时左右就下载完了！）

* 建立一个目录来存放 Android 源码

```
$ mkdir android_source_5.0
$ cd android_source_5.0
```
* 初始化下载

```
$ repo init -u https://android.googlesource.com/platform/manifest -b 
你要同步的源码(android-5.0.1_r1)
```
* 在初始化时，可能要配置下git的emai 信息：

```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```
* Downloading the Android Source Tree同步源码

```
$ repo sync
```
这个命令如果代理断了需要重新执行这个命令同步代码(也可以写一个shell 脚本)

当控制台显示 successful的时候恭喜你代码同步成功了~控制台显示如下：


### 编译源代码

进入到你DownLoad 源码的目录下，执行以下命令

```
$ cd android_source_5.0
$ source build/envsetup.sh
$ choosecombo release aosp_flo eng
$ make -j4
```

编译的时间是漫长的，中间还可能出错，所以我们需要随时陪同，中间还可能需要解错，当你看见屏幕的最下面出现 Successful 的时候，恭喜你，源码已经编译成功。如下图：
![这里写图片描述](https://img-blog.csdn.net/20160325114724525)

> 转载请注明出处：http://blog.csdn.net/u011974987/article/details/50978291







