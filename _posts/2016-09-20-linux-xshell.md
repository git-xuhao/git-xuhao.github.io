---
layout: post
title: Xshell 远程连接Linux服务器
date: 2016-09-20 18:30:31
categories: Linux
tags: Linux Xshell
desc: 我这里有一台电脑，装的Ubuntu系统，当我想使用时，又要去使用另外一台机器（如果没有，可以直接去阿里云整一台主机），更何况文件传输也不方便，比较麻烦，后来，看着同事用的Xshell 远程连接linux，挺方便的就折腾了一番，当然，putty，xftp是个很不错的选择。
---


我这里有一台电脑，装的Ubuntu系统，当我想使用时，又要去使用另外一台机器（如果没有，可以直接去阿里云整一台主机），更何况文件传输也不方便，比较麻烦，后来，看着同事用的Xshell 远程连接linux，挺方便的就折腾了一番，当然，putty，xftp是个很不错的选择。

xshell和xftp是windows下访问ubuntu（linux）服务器的一个非常好使的工具。

#### **ubuntu安装ssh服务器**
首先，判断Ubuntu是否安装了ssh服务： 
1、输入：

```
#ps -e |grep ssh
```
 如果服务已经启动，则可以同时看到“ssh-agent”和“sshd”，否则表示没有安装服务，或没有开机启动 。
<!-- more -->

2、安装ssh服务，输入命令：

```
#sudo apt-get install openssh-server  
```
出现问题时，重启ssh服务：

```
sudo service ssh restart
```

3、启动服务:

```
#/etc/init.d/ssh start  
```

4、本机测试是否能够成功登录：

```
# ssh 用户名@本机ip地址

例如：
# ssh xuhao@192.168.xxx
```
最后测试成功如下：

```
Welcome to Ubuntu 15.10 (GNU/Linux 4.2.0-42-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

1 package can be updated.
0 updates are security updates.

Last login: Sun Sep 18 12:35:27 2016 from 192.168.xxx

```

#### **在Ubuntu 上查看其IP**

在windows 使用xshell连接 远程linux 双方必须是有网络的，不然无法连接。

输入：

```
$ ifconfig
```
如下图：

![这里写图片描述](https://img-blog.csdn.net/20160919180446941)

红色箭头指向的地方就是Ubuntu的IP。

#### **在windows 上查看其IP**

在管理员 dos命令控制台上输入

```
ipconfig
```

如下图：


![这里写图片描述](https://img-blog.csdn.net/20160919180921364)

#### **检测是否能互相ping通**

在windows 同dos命令下：

```
ping Ubuntu 的ip
```

在Ubuntu 下：

```
ping Windows的ip
```

查看他们是否能ping。

#### **配置Xshell远程登录**

如果没有Xshell的，去网上下载一个。

我们打开Xshell：

![这里写图片描述](https://img-blog.csdn.net/20160919182058291)


**参数说明：**

**名称：** 自己随便起个名字；
**协议：** 选择SSH协议；
**主机：** 填写Ubuntu的IP。

下面的重新连接，根据需要选择就行，

填完之后，点击OK，填写Ubuntu的密码，contect（连接）。最后就连接成功了。

![这里写图片描述](https://img-blog.csdn.net/20160919182711229)

连接成功就可以控制远程服务器了，然后在去下载一个Xftp，进行文件传输，很方便，哈哈。~~



 