---
layout: post
title: shell 输入/输出重定向（四）
permalink: linux/shell-script-04
date: 2016-10-01 12:23:31
categories: Shell
tags: Shell
desc: 原本计划国庆节第一天假期休息一天，但是想想自己还有很多事情没有完成，就立马打开电脑，开始干活了。打算这7天的假期好好整理下之前学的知识和巩固练习，充实每一天，好了入正题，Linux shell脚本基础前面我们在介绍Linux shell脚本的控制流程时，还有一部分内容没讲就是有关here document的内容这里继续，Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序
---

### 前言
原本计划国庆节第一天假期休息一天，但是想想自己还有很多事情没有完成，就立马打开电脑，开始干活了。打算这7天的假期好好整理下之前学的知识和巩固练习，充实每一天，好了入正题，Linux shell脚本基础前面我们在介绍Linux shell脚本的控制流程时，还有一部分内容没讲就是有关here document的内容这里继续，Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序。

原文链接请标明： 
[http://xuhaoblog.com/linux/shell-script-04.html](http://xuhaoblog.com/linux/shell-script-04.html)
本文出自:【[stromxu](http://xuhaoblog.com)的博客】

---

### 正文
[接着上一篇继续...](http://xuhaoblog.com/2016/09/30/linux/shell-script-03.html)

#### **1、Shell 输入/输出重定向**
##### 重定向命令列表如下：

| 命令 | 说明 |
| ------------- |:-------------|
| command > file | 将输出重定向到 file。 |
| command < file | 将输入重定向到 file。 |
| command >> file | 将输出以追加的方式重定向到 file。|
| n > file | 将文件描述符为 n 的文件重定向到 file。|
| n >> file | 将文件描述符为 n 的文件以追加的方式重定向到 file。|
| n >& m | 将输出文件 m 和 n 合并。|
| n <& m | 将输入文件 m 和 n 合并。|
|<< tag | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。|

<!-- more -->


 -  **需要注意的是文件描述符 0 通常是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。**

----
##### **1.1、输出重定向**
重定向一般通过在命令间插入特定的符号来实现，语法如下：

```
command1 > file1
```

上面这个命令执行command1然后将输出的内容存入file1。

注意任何file1内的已经存在的内容将被新内容替代。如果要将新的内容添加到文件的末尾，则使用>>操作符。

**例子**：

我们先新建一个文件test2，然后执行 ls -al 重定向到test2文件中，在命令行的操作符代码如下：

```
#新建test2文件
touch test2    
#输出重定向
ls -al > test2

```

然后我们使用cat命令查看文件内容：

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# cat test3
total 132
drwxr-xr-x 2 root root  4096 Oct  1 13:19 .
drwxr-xr-x 4 root root  4096 Sep 27 16:29 ..
-rw-r--r-- 1 root root 12288 Sep 27 19:15 .01.sh.swo
-rw-r--r-- 1 root root 12288 Sep 27 16:35 .01.sh.swp
-rwxr--r-- 1 root root   347 Sep 28 21:07 02.sh
-rwxr--r-- 1 root root   100 Sep 28 21:28 03.sh
-rw-r--r-- 1 root root 12288 Sep 28 21:15 .03.sh.swp
-rwxr--r-- 1 root root   109 Sep 28 22:17 04.sh
-rw------- 1 root root 12288 Sep 30 18:03 .04.swp
-rw-r--r-- 1 root root     0 Sep 28 22:45 05.sh
-rw-r--r-- 1 root root 12288 Sep 28 22:47 .05.sh.swp
-rw-r--r-- 1 root root     0 Sep 30 20:57 06.sh
-rw-r--r-- 1 root root 12288 Sep 30 21:00 .06.sh.swp
-rwxrwxrwx 1 root root   223 Sep 30 17:50 b.sh
-rw-r--r-- 1 root root 12288 Sep 30 18:03 .b.sh.swo
-rw-r--r-- 1 root root 12288 Sep 28 16:50 .b.sh.swp
-rwxrwxrwx 1 root root   119 Oct  1 13:12 demo.sh
-rwxr--r-- 1 root root    68 Sep 30 14:09 test1.sh
-rw-r--r-- 1 root root   954 Oct  1 13:10 test2
-rw-r--r-- 1 root root     0 Oct  1 13:19 test3

```

就会把当前目录下的所有文件给显示出来了。

如果不希望文件内容被覆盖，可以使用 >> 追加到文件末尾，这个请自行练习使用。

---

##### **1.2、输入重定向**
和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：

```
command1 < file1
```

这样，本来需要从键盘获取输入的命令会转移到文件读取内容。注意：输出重定向是大于号（>），出入重定向是小于号（<）。

**例子**

接着上面的例子，我们统计test2文件的行数，(不熟悉命令的同学，请查看我的 [shell命令（二）](http://blog.csdn.net/u011974987/article/details/52703177)的文章），执行以下命令：

```
 wc -l test2
 21 test2
```
也可以将输入重定向到test2文件中：

```
wc -l < test2
21
```

**注意：**上面两个例子的结果不同；第一个例子，会输出文件名；第二不会，因为它仅仅知道从标准输入读取内容。

```
 command1 < infile > outfile
```

同时替换输入和输出，执行command1，从文件infile读取内容，然后将输出写入到outfile中。

---
##### **1.3、重定向深入理解**

一般情况下，每一个Linux 命令运行时都会打开三个文件：

 - 标准输入文件（stdin）: stdin的文件描述符为0，linux程序默认从stdin读取数据。
 - 标准输出文件（stdout）: stdout 的文件描述为1，linux程序默认想stdout输出数据。
 - 标准错误文件（stderr）: stderr 的文件描述符为2，linux程序会想stderr流中写入错误信息。

---
 以后打开文件后。新增文件绑定描述符 可以依次增加。 一条shell命令执行，都会继承父进程的文件描述符。因此，所有运行的shell命令，都会有默认3个文件描述符。 


正常情况下，command > file 将stdout重定向file，command < file 将stdin 重定向到 file。

一个命令执行了：
先有一个输入：输入可以从键盘，也可以从文件得到
命令执行完成：成功了，会把成功结果输出到屏幕：standard output默认是屏幕
命令执行有错误：会把错误也输出到屏幕上面：standard error默认也是指的屏幕

如果希望stderr 重定向 到file 可以这样写：

```
command 2> file
```

如果希望stderr追加到file 文件末尾，可以这样写：

```
command 2>> file
```

 - **实例（通俗易懂）**

显示当前目录文件，写一个实际存在和一个不存在的，方便理解。比如当前目录存在test1.sh，不存在xiho。代码如下：
```
root@iZ28t5k4ny4Z:/usr/xiho/shell# ls test1.sh xiho 
ls: cannot access xiho: No such file or directory
test1.sh
```

看到上面代码显示在屏幕上面，找不到文件路劲报错，正确输出与错误输出都显示在屏幕了，**但是现在需要把正确输出写入suc.txt， 1> 可以省略，不写，默认所至标准输出，把错误输出到err.txt，不输出到屏幕。**

例子如下：

```
ls test1.sh xiho 1> suc.txt 2>err.txt

```
然后我们分别查看suc.txt和err .txt的信息。

![这里写图片描述](https://img-blog.csdn.net/20161001150059408)

将错误输出信息关闭掉

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# ls test1.sh xiho 2>&-
test1.sh
root@iZ28t5k4ny4Z:/usr/xiho/shell# ls test1.sh xiho 2>/dev/null
test1.sh

&[n] 代表是已经存在的文件描述符，&1 代表输出 &2代表错误输出 &-代表关闭与它绑定的描述符
/dev/null 这个设备，是linux 中黑洞设备，什么信息只要输出给这个设备，都会给吃掉，哈哈~
```
其他的追加操作符等，好玩的自己去试试。

**注意：**
1、shell遇到”>”操作符，会判断右边文件是否存在，如果存在就先删除，并且创建新文件。不存在直接创建。 无论左边命令执行是否成功。右边文件都会变为空。
2、“>>”操作符，判断右边文件，如果不存在，先创建。以添加方式打开文件，会分配一个文件描述符[不特别指定，默认为1,2]然后，与左边的标准输出（1）或错误输出（2） 绑定。
3、当命令：执行完，绑定文件的描述符也自动失效。0,1,2又会空闲。
4、一条命令启动，命令的输入，正确输出，错误输出，默认分别绑定0,1,2文件描述符。
5、一条命令在执行前，先会检查输出是否正确，如果输出设备错误，将不会进行命令执行。

----

#### **2、Here Document**

Here Document是shell 中的一种特殊重定向方式，用来将输入重定向到一个交互式的shell脚本或程序。

基本的格式如下：

```
command << EOF
	document
EOF
```

它的作用是将两个EOF之间的内容（docuemnt）作为输入传递给command。

> 注意：
>  - 结尾的EOF一定要顶个写，前面不能有任何字符，后面也不能有任何字符，包括空格和tab缩进。
>  - 开始的EOF前后的空格不要被忽略掉。

 - **实例**

在命令行中通过wc -l 命令计算Here Document 的行数：

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# wc -l << EOF
> 学习使用shell 编程
> www.xuhaoblog.com
> EOF
2
```
输出的结果为2行。

我们也可以将Here Document 用在脚本中，例如：

```
1 #!/bin/bash                                                            
  2 cat << EOF
  3 学习使用shell脚本编程
  4 www.xuhaoblog.com
  5 EOF

```
执行以上脚本输出的结果：

```
root@iZ28t5k4ny4Z:/usr/xiho/shell# chmod u+x 05.sh 
root@iZ28t5k4ny4Z:/usr/xiho/shell# ./05.sh
学习使用shell脚本编程
www.xuhaoblog.com
```

Here Document 的简单使用就到这里，还有更多的操作符需要我们去练习。Linux shell脚本基础学习，我们差不多介绍完了，还有一个关于函数的，准备放到下一篇来讲。欢迎关注~

----
### 更多相关系列传送门： 
**【个人博客】shel 脚本系列**

 - [【Linux 系统编程】shell 脚本基础学习（一）](http://xuhaoblog.com/linux/shell-script-01.html)
 - [【Linux 系统编程】shell 命令和流程控制（二）](http://xuhaoblog.com/linux/shell-script-02.html)
 - [【Linux 系统编程】shell 流程控制loop和引号（三）](http://xuhaoblog.com/linux/shell-script-03.html)
 - [【Linux 系统编程】shell 输入/输出重定向（四）](http://xuhaoblog.com/linux/shell-script-04.html)
 - [【Linux 系统编程】shell 脚本基础学习之函数（五）](http://xuhaoblog.com/linux/shell-script-05.html)

