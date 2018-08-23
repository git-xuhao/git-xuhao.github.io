---
layout: post
title: vim 的安装配置及使用
permalink: linux/vim-config
date: 2016-09-29 12:22:31
categories: Linux
tags: Linux vim
desc: Learn vim and it will be your last text editor. There isn’t any better text editor that I know of. It is hard to learn, but incredible to use.
---

**前言**
------

Learn vim and it will be your last text editor. There isn’t any better text editor that I know of. It is hard to learn, but incredible to use.
学习VIM，它会是你最后的文本编辑器。没有任何好转的文本编辑器，我知道的。这是很难学，但令人难以置信的使用。

VI是LINUX下最老牌的编辑工具，而VIM则是VI的升级版本，LINUX系统必备啊~~


-------

**摘要**
--

**（以下已Ubuntu系统为例）**

#### **VIM的安装和配置**

##### **1、VIM安装**
既然VIM编辑器是在Linux 系统环境下运行的，那么我们首先要在Ubuntu上安装VIM；
在命令行敲入“vi”后按"tab"键，可以查看到目前系统中有没有安装Vim，如果没有的话就只会显示vi和vim.tiny。如下图(我是已经安装了的）

![这里写图片描述](https://img-blog.csdn.net/20160929104119636)

如果没有安装VIM，普通用户下输入命令：

```
sudo apt-get install vim-gtk
```
然后按照提示，输入“y”后，回车，之后不要进行任何操作，等待安装完成。安装完成后，在命令行输入vi，按“tab”键。如上图，说明vim已经安装成功~
<!-- more -->

##### **2、VIM配置**

刚安装的VIM，默认的配置可能不是很好使用，这就需要我们去配置VIM，在命令下，输入命令：

```
sudo vim /etc/vim/vimrc
```
必须加上sudo，否则你是没有权限编辑vimrc的。

在这个文件中，会有这么一句：
**syntax on**
意思是语法高亮，如果您的被注释掉了，请“让它出来”，就如下图所示：

![这里写图片描述](https://img-blog.csdn.net/20160929105720795)

接着我们在VIM配置文件的最后一行，加入以下配置，会使我们方便很多。

```
set nu                           // 在左侧行号
set tabstop                  //tab 长度设置为 4
set nobackup               //覆盖文件时不备份
set cursorline               //突出显示当前行
set ruler                       //在右下角显示光标位置的状态行
set autoindent             //自动缩进
```

![这里写图片描述](https://img-blog.csdn.net/20160929110041781)

保存之后，配置完毕。
上面的配置，其实是非常简单的，比如一些配色方案等，小编并没有写入，如果您还有其他需求的话，建议多查些资料。


----


#### **VIM常用的基本命令**

```
1. vim #在命令行中输入vim,进入vim编辑器 

2. i #按一下i键,下端显示 --INSERT-- 
	#插入命令,在vim中可能任意字符都有作用 

3. Esc 
#退出i(插入)命令进行其它命令使用

4. :r filename 
#读入一个文件内容,并写入到当前编辑器中 

5. :w newfilename 
#将该编辑器中的内容写入到一个新文件中 

6. :w 
#在编辑的过程中保存文件,相当于word中的ctrl+s 

7. :! command 
暂时离开 vi 到指令列模式下执行 command 的显示结果！例如 :!ls 
#在编辑过程中执行shell命令ls 

8. :sh 
#进入shell命令行,执行完命令后ctrl+d退出重新进入vim编辑继续编辑 在shell命令下，执行ctral+l完成清屏 

9. :wq 
#保存文件并退出 

10. ZZ 
#保存文件并退出,同上一个命令,注意大写 

11. :q! 
#强制退出,不保存 

12. :set number或者：set nu 
#使编辑中的文件显示行号 

13. :set nonumber或者：set nonu  
#与上一条命令相反,不显示行号 

14. :help i 
#查看插入命令帮助 

15. u  
#撤消上一步操作 

16. /Fedora 
#查找Fedora字符 

17. :s /Fedora/Redhat 
#将Fedora字符替换为Redhat(只替换在光标所在的行) 

18. dw #删除单词 
    dd #删除行 

19. o 
#打开空白一行 

20. vim + filename 
#进行文件最后一行进行编辑 

21. vim +n filename 
#进入文件第n行进行编辑 

22. :1,.s/redhat/fedora 
#.号表示当前行,即光标所在行 
#将第1行到当前行(.)第一次出现的redhat字符代替为fedora 

23. :1,.s/redhat/fedora/g 
#将第1行到当前行(.)所有出现的redhat字符代替为fedora,g 全局标志 

24. :1,$s/redhat/fedora/g 
#$表示最后一行 
#将第1行到最后一行所有出现的redhat字符代替为fedora 

25. :%s/redhat/fedora/g #同上一个命令 

26. :%s/\/fedora/g 
#将第1行到最后一行所有出现的redhat字代替为fedora #字,而不是字符

27. :f 
#显示文件内容,状态等等 #同ctrl+g命令 

28. :e! 
#当前文件,返回到上次保存 :e file 
#切换编辑文件 

29. :n 
#当编辑时有多个文件(比如vim file1 file2)时切换到下一个文件,与:e file 结合使用
```
