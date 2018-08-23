---
layout: post
title: shell 脚本基础学习之函数（五）
permalink: linux/shell-script-05
date: 2016-10-01 18:10:31
categories: Shell
tags: Shell
desc: Linux shell脚本基础学习我们这里就差不多讲完了，函数相信大家都不陌生吧，直接入正题吧~
---

## 前言
Linux shell脚本基础学习我们这里就差不多讲完了，函数相信大家都不陌生吧，直接入正题吧~


## 正文
[继上篇博客之后](http://xuhaoblog.com/linux/shell-script-04.html)
#### **shell 函数**

Linux shell 可以用户定义函数，然后在shell脚本中可以随便调用。
shell 中函数的定义格式如下：

```
function_name () {
    list of commands
    [ return value ]
}
```
说明：
 
 - 1、函数名前面也可以加上关键字function；
 - 2、函数的返回值，可以显示加：return 返回，如果不加，将以最后一条命令的运行结果作为返回值。
 <!-- more -->
shell 函数返回值只能是整数，一般用来表示函数执行是否政工，0表示成功，其他则表示失败。

如果要让函数返回字符串，那么可以先定义一个变量，用来接收函数的计算结果，脚本在访问这个变量来获得函数的返回值。

比如：

```
  1 #!/bin/bash                                                            
  2 Hello () {
  3         echo "My name is xu hao"
  4 }
  5 
  6 #调用上面的函数
  7 Hello
```
运行结果：

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# ./06.sh 
My name is xu hao
```

**调用函数只需要给出函数名，不需要加括号。**

再来看一个带有return语句的函数：

在上面例子后面添加：

```
 funWithReturn() {
 11         echo "这个函数功能是得到两个数字的和"
 12         echo -n "请输入第一个数字"
 13         read aNum;
 14         echo -n "请输入第二个数字"
 15         read bNum;
 16         echo "这两个数分别是 $aNum 和 $bNum !"
 17   return $(($aNum+$bNum))
 18 } 
 19 
 20 #调用funWithReturn这个函数
 21 funWithReturn
 22 
 23 ret=$?
 24 echo "The sun of two numbers is $ret !"

```

运行结果：

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# ./06.sh 
My name is xu hao
这个函数功能是得到两个数字的和
请输入第一个数字56
请输入第二个数字23
这两个数分别是 56 和 23 !
The sun of two numbers is 79 !

```

> $?最后运行的命令的结束代码（返回值），这个在后面会总结的。


---

#### **函数参数**

```
在shell 中，调用函数时可以向其传递参数，在函数体内部，通过$n 的形式来获取参数，比如,$1表示第一个参数，$2表示第二个参数
```

再来看一个带参数的函数例子：

```
 28 funWithParam() {
 29         echo "第一个param为 $1"
 30         echo "第二个param为 $2"
 31         echo "第十个param为 $10"
 32         echo "第十个param为 ${10}"
 33         echo "参数总共有 $#"
 34         echo "作为一个字符串输出所有参数 $* !"
 35 
 36 }
 37 #调用函数并传递参数
 38 funWithParam 1 2 3 4 5 6 23 45 22 9 25

```

运行脚本输出的结果为：

![这里写图片描述](https://img-blog.csdn.net/20161001175800334)

```
 在这里注意下，$ 10不能获取第10个参数，当n>=10时，需要${n}来获取参数。
```

**另外：在补充几个特殊字符来处理的参数**

```
$#	传递到脚本的参数个数
$*	以一个单字符串显示所有向脚本传递的参数
$$	脚本运行的当前进程ID号
$!	后台运行的最后一个进程的ID号
$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。
$-	显示Shell使用的当前选项，与set命令功能相同。
$?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
```

一下午整理这两篇博客，算是把Linux shell 脚本的基础的理论学习讲完了，更多的细节只有等我们遇到了在慢慢琢磨吧！吃饭~

---

## 更多相关系列传送门： 
**【个人博客】shel 脚本系列**

 - [【Linux 系统编程】shell 脚本基础学习（一）](http://xuhaoblog.com/linux/shell-script-01.html)
 - [【Linux 系统编程】shell 命令和流程控制（二）](http://xuhaoblog.com/linux/shell-script-02.html)
 - [【Linux 系统编程】shell 流程控制loop和引号（三）](http://xuhaoblog.com/linux/shell-script-03.html)
 - [【Linux 系统编程】shell 输入/输出重定向（四）](http://xuhaoblog.com/linux/shell-script-04.html)
 - [【Linux 系统编程】shell 脚本基础学习之函数（五）](http://xuhaoblog.com/linux/shell-script-05.html)




