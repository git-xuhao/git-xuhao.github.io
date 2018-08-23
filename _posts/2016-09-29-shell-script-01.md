---
layout: post
title: shell 脚本基础学习（一）
permalink: linux/shell-script-01
date: 2016-09-29 13:06:31
categories: Shell
tags: Shell
desc: Linux shell 脚本基础我们将分几篇文章来记录，希望对我们这些初次接触Linxu shell脚本编程的初学者有帮助；很多时候我们拿到别人的开源库，需要编译使用，但是由于各种原因，总是编译不成功，其中就有shell 脚本，如果我们看不懂别人的脚本，编译出错，还查不来原因。（比如一个空格导致编译失败，你能查出原因么？）
---

## 前言 ##
Linux shell 脚本基础我们将分几篇文章来记录，希望对我们这些初次接触Linxu shell脚本编程的初学者有帮助；
Linxu shell 脚本基础学习先来介绍shell的语法，开头、注释、变量和环境，虽然不涉及到具体的东西，但是大号基础是以后学习的前提，很多时候我们拿到别人的开源库，需要编译使用，但是由于各种原因，总是编译不成功，其中就有shell 脚本，如果我们看不懂别人的脚本，编译出错，还查不来原因。（比如一个空格导致编译失败，你能查出原因么？）

## 正文 ##

### 1.Linux 脚本编写基础

#### 1.1 语法的基本介绍
##### **1.1.1 开头**
程序必须以下面的行开始（必须放在文件的第一行）：

```
#!/bin/bash
```
符号"#!"用来告诉系统它后面的参数是用来执行该文件的程序。在这个例子中我们使用/bin/sh来执行程序。
当编辑好脚本时，如果要执行该脚本，还必须使其可执行。
要使脚本可执行：
编译 chmod  u+x filename 这样才能用./filename 来运行

##### **1.1.2 注释**

在进行shell编程时，以#开头的句子表示注释，直到这一行的结束。我们真诚地建议您在程序中使用注释。
如果您使用了注释，那么即使相当长的时间内没有使用该脚本，您也能在很短的时间内明白该脚本的作用及工作原理。
<!-- more -->

##### **1.1.3 变量**

在其他编程语言中您必须使用变量。在shell编程中，所有的变量都由字符串组成，并且您不需要对变量进行声明。要赋值给一个变量，您可以这样写：

```
#!/bin/sh
#对变量赋值：
a="hello world"
# 现在打印变量a的内容：
echo "A is:"
echo $a
```
有时候变量名很容易与其他文字混淆，比如：
```
num=2
echo "this is the $numnd"
```
这并不会打印出"this is the 2nd"，而仅仅打印"this is the "，因为shell会去搜索变量numnd的值，但是这个变量时没有值的。可以使用花括号来告诉shell我们要打印的是num变量：
```
num=2
echo "this is the ${num}nd"
```
这将打印： this is the 2nd

##### **1.1.4 环境变量**
由export关键字处理过的变量叫做环境变量。我们不对环境变量进行讨论，因为通常情况下仅仅在登录脚本中使用环境变量。

这一篇就介绍到这里，下面我们会接触到具体的Linux shell脚本基础的实质部分。

---
## 更多相关系列传送门： 
**【个人博客】shel 脚本系列**

 - [【Linux 系统编程】shell 脚本基础学习（一）](http://xuhaoblog.com/linux/shell-script-01.html)
 - [【Linux 系统编程】shell 命令和流程控制（二）](http://xuhaoblog.com/linux/shell-script-02.html)
 - [【Linux 系统编程】shell 流程控制loop和引号（三）](http://xuhaoblog.com/linux/shell-script-03.html)
 - [【Linux 系统编程】shell 输入/输出重定向（四）](http://xuhaoblog.com/linux/shell-script-04.html)
 - [【Linux 系统编程】shell 脚本基础学习之函数（五）](http://xuhaoblog.com/linux/shell-script-05.html)

