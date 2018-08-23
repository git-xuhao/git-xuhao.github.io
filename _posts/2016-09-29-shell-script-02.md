---
layout: post
title: shell 命令和流程控制（二）
permalink: linux/shell-script-02
date: 2016-09-29 16:06:31
categories: Shell
tags: Shell
desc: Linxu shell 脚本基础这里介绍到第二篇，详细介绍shell 命令和流程控制，这部分介绍三类命令，在学习时大家应该加以比较学习。
---

## 前言
Linxu shell 脚本基础这里介绍到第二篇，详细介绍shell 命令和流程控制，这部分介绍三类命令，在学习时大家应该加以比较学习。
Linux shell脚本基础课程[前面一篇](http://xuhaoblog.com/linux/shell-script-01.html)介绍的都是语法基础的开头、注释、变量和 环境变量，这里将介绍shell命令和控制流程的第一部分，在shell脚本中可以使用三类命令，而控制流程就放在下一讲吧。

---

## 正文

## **1.1.5 Shell命令和流程控制**

在shell脚本中可以使用三类命令：

### **1)Unix 命令:**
虽然在shell脚本中可以使用任意的unix命令，但是还是由一些相对更常用的命令。这些命令通常是用来进行文件和文字操作的。
<!-- more -->
常用命令语法及功能

```
echo "some text": 将文字内容打印在屏幕上

ls: 文件列表

wc –l filewc -w filewc -c file: 计算文件行数计算文件中的单词数计算文件中的字符数

cp sourcefile destfile: 文件拷贝

mv oldname newname : 重命名文件或移动文件

rm file: 删除文件

grep 'pattern' file: 在文件内搜索字符串比如：grep 'searchstring' file.txt

cut -b colnum file: 指定欲显示的文件内容范围，并将它们输出到标准输出设备比如：输出每行第5个到第9个字
符cut -b5-9 file.txt千万不要和cat命令混淆，这是两个完全不同的命令

cat file.txt: 输出文件内容到标准输出设备（屏幕）上

file somefile: 得到文件类型

read var: 提示用户输入，并将输入赋值给变量

sort file.txt: 对file.txt文件中的行进行排序

uniq: 删除文本文件中出现的行列比如： sort file.txt | uniq

expr: 进行数学运算Example: add 2 and 3expr 2 "+" 3

find: 搜索文件比如：根据文件名搜索find . -name filename -print

tee: 将数据输出到标准输出设备(屏幕) 和文件比如：somecommand | tee outfile

basename file: 返回不包含路径的文件名比如： basename /bin/tux将返回 tux

dirname file: 返回文件所在路径比如：dirname /bin/tux将返回 /bin

head file: 打印文本文件开头几行

tail file : 打印文本文件末尾几行

sed: Sed是一个基本的查找替换程序。可以从标准输入（比如命令管道）读入文本，并将
结果输出到标准输出（屏幕）。该命令采用正则表达式（见参考）进行搜索。不要和shell中的通配符相混淆。比如：将linuxfocus 替换为LinuxFocus ：cat text.file | sed 's/linuxfocus/LinuxFocus/' > 

newtext.fileawk: awk 用来从文本文件中提取字段。缺省地，字段分割符是空格，可以使用-F指定其他分割符。

cat file.txt | awk -F, '{print $1 "," $3 }'这里我们使用，作为字段分割符，同时打印第一个和第三个字段。如果该文件内容如下： Adam Bor, 34, IndiaKerry Miller, 22, USA命令输出结果为：Adam Bor, IndiaKerry Miller, USA

```

---
### **2) 概念: 管道, 重定向和 backtick**

<font color=red>这些不是系统命令，但是他们真的很重要。</font>

**管道 (|)：**
 将一个命令的输出作为另外一个命令的输入。

```
grep "hello" file.txt | wc -l
```

在file.txt中搜索包含有”hello”的行并计算其行数。
在这里grep命令的输出作为wc命令的输入。当然您可以使用多个命令。

**重定向：**
将命令的结果输出到文件，而不是标准输出（屏幕）。

```
> 写入文件并覆盖旧文件
>> 加到文件的尾部，保留旧文件内容。
```

**反短斜线：**
使用反短斜线可以将一个命令的输出作为另外一个命令的一个命令行参数。
命令：

```
find . -mtime -1 -type f -print
```
用来查找过去24小时（-mtime –2则表示过去48小时）内修改过的文件。如果您想将所有查找到的文件打一个包，则可以使用以下脚本：

```
#!/bin/sh
# The ticks are backticks (`) not normal quotes ('):
tar -zcvf lastmod.tar.gz `find . -mtime -1 -type f -print`
```

---

### **3) 流程控制**


#### **1.if**
"if" 表达式 如果条件为真则执行then后面的部分：
```
if ....; then
....
elif ....; then
....
else
....
fi
```
大多数情况下，可以使用测试命令来对条件进行测试。比如可以比较字符串、判断文件是否存在及是否可读等等…

通常用" [ ] "来表示条件测试。注意这里的空格很重要。要确保方括号的空格。

```
[ -f "somefile" ] ：判断是否是一个文件
[ -x "/bin/ls" ] ：判断/bin/ls是否存在并有可执行权限
[ -n "$var" ] ：判断$var变量是否有值
[ "$a" = "$b" ] ：判断$a和$b是否相等
```

执行man test可以查看所有测试表达式可以比较和判断的类型。
直接执行以下脚本：

```
#!/bin/sh
if [ "$SHELL" = "/bin/bash" ]; then
echo "your login shell is the bash (bourne again shell)"
else
echo "your login shell is not bash but $SHELL"
fi
```
变量$SHELL包含了登录shell的名称，我们和/bin/bash进行了比较。
快捷操作符
熟悉C语言的朋友可能会很喜欢下面的表达式：

```
[ -f "/etc/shadow" ] && echo "This computer uses shadow passwors"
```

这里 && 就是一个快捷操作符，如果左边的表达式为真则执行右边的语句。


您也可以认为是逻辑运算中的与操作。上例中表示如果/etc/shadow文件存在则打印” This computer uses shadow passwors”。同样或操作(||)在shell编程中也是可用的。这里有个例子：

```
#!/bin/sh
mailfolder=/var/spool/mail/james
[ -r "$mailfolder" ]' '{ echo "Can not read $mailfolder" ; exit 1; }
echo "$mailfolder has mail from:"
grep "^From " $mailfolder
```
该脚本首先判断mailfolder是否可读。如果可读则打印该文件中的"From" 一行。如果不可读则或操作生效，打印错误信息后脚本退出。这里有个问题，那就是我们必须有两个命令：
- 打印错误信息
- 退出程序
我们使用花括号以匿名函数的形式将两个命令放到一起作为一个命令使用。一般函数将在下文提及。
不用与和或操作符，我们也可以用if表达式作任何事情，但是使用与或操作符会更便利很多。


#### **2.case**
**case :**表达式可以用来匹配一个给定的字符串，而不是数字。

```
case ... in
...) do something here ;;
esac
```

让我们看一个例子。 file命令可以辨别出一个给定文件的文件类型，比如：
file lf.gz
这将返回：

```
lf.gz: gzip compressed data, deflated, original filename,
last modified: Mon Aug 27 23:09:18 2001, os: Unix
```

我们利用这一点写了一个叫做smartzip的脚本，该脚本可以自动解压bzip2, gzip 和zip 类型的压缩文件：

```
#!/bin/sh
ftype=`file "$1"`
case "$ftype" in
"$1: Zip archive"*)
unzip "$1" ;;
"$1: gzip compressed"*)
gunzip "$1" ;;
"$1: bzip2 compressed"*)
bunzip2 "$1" ;;
*) echo "File $1 can not be uncompressed with smartzip";;
esac

```

您可能注意到我们在这里使用了一个特殊的变量$1。该变量包含了传递给该程序的第一个参数值。
也就是说，当我们运行：

```
smartzip articles.zip
```

$1 就是字符串 articles.zip

#### **3. selsect**
select 表达式是一种bash的扩展应用，尤其擅长于交互式使用。用户可以从一组不同的值中进行选择。

```
select var in ... ; do
break
done	
.... now $var can be used ....
```
下面是一个例子：

```
#!/bin/sh
echo "What is your favourite OS?"
select var in "Linux" "Gnu Hurd" "Free BSD" "Other"; do
break	
done
echo "You have selected $var"
```

下面是该脚本运行的结果：

```
What is your favourite OS?
1) Linux
2) Gnu Hurd
3) Free BSD
4) Other
#? 1
You have selected Linux
```
上面就是这一篇的内容，控制流程比较多，这里先介绍这三个。下篇继续。。。

---
## 更多相关系列传送门： 
**【个人博客】shel 脚本系列**

 - [【Linux 系统编程】shell 脚本基础学习（一）](http://xuhaoblog.com/linux/shell-script-01.html)
 - [【Linux 系统编程】shell 命令和流程控制（二）](http://xuhaoblog.com/linux/shell-script-02.html)
 - [【Linux 系统编程】shell 流程控制loop和引号（三）](http://xuhaoblog.com/linux/shell-script-03.html)
 - [【Linux 系统编程】shell 输入/输出重定向（四）](http://xuhaoblog.com/linux/shell-script-04.html)
 - [【Linux 系统编程】shell 脚本基础学习之函数（五）](http://xuhaoblog.com/linux/shell-script-05.html)
