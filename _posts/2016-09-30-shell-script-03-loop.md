---
layout: post
title: shell 流程控制loop和引号（三）
permalink: linux/shell-script-03
date: 2016-09-30 11:06:31
categories: Shell
tags: Shell
desc: Linux shell 脚本基础学习第三篇,Liunx shell 脚本基础学习中，我们讲到了Linux shell 脚本中控制流程的if、select、case，这里接着介绍Linux shell 脚本控制流程的loop和引导，控制流程这部分内容较多，还有一部分是关于here document。
---


## 前言
Linux shell 脚本基础学习第三篇；
[上一篇](http://xuhaoblog.com/linux/shell-script-02.html)Liunx shell  脚本基础学习中，我们讲到了Linux shell 脚本中控制流程的if、select、case，这里接着介绍Linux shell 脚本控制流程的loop和引导，控制流程这部分内容较多，还有一部分是关于here document。

----

## 正文
[继上篇之后](http://xuhaoblog.com/linux/shell-script-02.html)
#### **4、loop**

loop 表达式：

```
while ...; do
...
done
```
while- loop 将运行一直到表达式测试为真。
（will run while the expression that we test for is true.）

关键字“break”用来跳出循环。而关键字“continue”用来不执行余下的部分而直接太偶倒下一个循环。

for-loop 表达式查看一个字符串列表（字符串用空格分隔）然后将其赋给一个变量：

```
for var in ....; do
....
done

```
<!-- more -->
在下面的例子中，将分别打印ABC到屏幕上：

```
#!/bin/sh
for var in A B C ; do
echo "var is $var"
done
```
下面是一个更为有用的脚本**showrpm**，其功能是打印一些RPM包的统计信息：

```
#!/bin/sh
# list a content summary of a number of RPM packages
# USAGE: showrpm rpmfile1 rpmfile2 ...	
# EXAMPLE: showrpm /cdrom/RedHat/RPMS/*.rpm

for rpmpackage in $*; do
if [ -r "$rpmpackage" ];then
echo "=============== $rpmpackage =============="
rpm -qi -p $rpmpackage
else
echo "ERROR: cannot read file $rpmpackage"
fi
done

```

这里出现了第二个特殊的变量$*，该变量包含了所有输入的命令行参数值。
如果您运行

```
showrpm openssh.rpm w3m.rpm webgrep.rpm
```

**此时 $* 包含了 3 个字符串，即openssh.rpm, w3m.rpm and webgrep.rpm**

----

#### **5、引号**

在想程序传递任何参数之前，程序会扩展通配符和变量。这里所谓的扩展的意思是程序会把通配符（比如*）替换成合适的文件名，它变量替换成变量值。为了防止程序做这种替换，您可以使用引号。

**下面是shell引用类型:**

 - ""  双引号
 - ''   单引号
 - `	反引号
 - \	反斜线

##### **5.1、双引号**
使用双引号，可引用除了字符$、`、\外的任意字符或字符串。
例如：
```
#!/bin/bash
STR="My name is xuhao"
echo "$STR"
```

输出：

```
My name is xuhao
```
也可以去掉双引号：

```
echo $STR
```
执行脚本输出的结果还是一样的。

双引号可以使具有特殊含义的词失去其原本的意义，如下：

![这里写图片描述](https://img-blog.csdn.net/20160930140550992)

特殊含义的cal,执行的是显示出2016年 12月的日历。

但是我们将cal 命令复制给一个变量，如下：

```
CAL="cal"
echo $CAL
```
最后输出cal
这里，双引号使cal 命令失去了原有的显示日历的功能；在这里双引号类似于转义字符。

##### **5.2、单引号**

　　单引号与双引号类似，不同的是shell 会忽略任何引用值，即屏蔽的单引号内的特殊字符的原本含义。

```
echo ‘my name is $LOGNAME’
其结果为：
my name is $LOGNAME
```

##### **5.3、单引号和双引号的区别**

单引号‘’：取消除单引号以外的任何字符的特殊含义。
如：

```
`echo ‘my name is $name`’
```

其结果为：

```
my name is $name
此时$只作为一个普通字符使用了。 
双引号“”：取消除双引号、$号以及_号以外的所有字符的特殊含义
```

单引号是强引用，引号里的值是什么，变量的值就是什么；

双引号是弱引用，引号里的值若再包含变量，那在赋值的时候，所有这些变量就被立即替换了。


##### **5.4、反引号**

反引号（`）用于设置系统命令输出到变量，shell认为反引号中的内容是一个系统命令，
所以将会执行之：

```
echo `date`
Mon Dec 24 16:06:55 CST 2012
```

这和直接输入date命令是一样的：

```
date
Mon Dec 24 16:07:12 CST 2012
```

若输入一个非系统命令，则shell不认识此命令，将会报错：
```
echo `DATE`
ksh: DATE: not found
```


##### **5.5、反斜杠**

反斜杠起的作用是将一些特殊字符按原样输出，这些特殊字符有：

```
$、*、`、+、^、&、|、"、?。
```

例如：echo *
将当前目录下的文件名和目录名都列出来。

```
echo \*
*
```

而使用反斜杠转义之后，就输出了星号。

---
## 更多相关系列传送门： 
**【个人博客】shel 脚本系列**

 - [【Linux 系统编程】shell 脚本基础学习（一）](http://xuhaoblog.com/linux/shell-script-01.html)
 - [【Linux 系统编程】shell 命令和流程控制（二）](http://xuhaoblog.com/linux/shell-script-02.html)
 - [【Linux 系统编程】shell 流程控制loop和引号（三）](http://xuhaoblog.com/linux/shell-script-03.html)
 - [【Linux 系统编程】shell 输入/输出重定向（四）](http://xuhaoblog.com/linux/shell-script-04.html)
 - [【Linux 系统编程】shell 脚本基础学习之函数（五）](http://xuhaoblog.com/linux/shell-script-05.html)

