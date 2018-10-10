---
layout: post
title: Git 本地提交未Push 自动切到其他版本的解决姿势
permalink: git-not_push
date: 2016-10-24 18:05:00
categories: GitHub
tags: Git
desc: 在现有的分支上进行修改代码，使用git提交本地代码后，SourceTree上面没有记录，我就纳闷了，我原本以为我提交到其他项目了，仔细查看了几个项目的提交记录也都没有，于是退出SourceTree，再打开这个项目还是没记录...
---


### **现象描述：**

在现有的分支上进行修改代码，使用git提交本地代码后，SourceTree上面没有记录，我就纳闷了，我原本以为我提交到其他项目了，仔细查看了几个项目的提交记录也都没有，于是退出SourceTree，再打开这个项目还是没记录，并且最新修改的分支在两个版本之前的Develop v2.2.2分支（这个我记得我是在最新的分支上开发的，不排除我在其他分支上进行开发的情况），不过这个都不是事儿，怎么恢复修改的代码到最新的分支上面呢？下面提供解决方案~~

### **解决方案：**

1. 打开**gitbash** 命令行；
2. 输出**git log** 查看不到提交历史的历史记录；  
3. 输入**git reflog**可以查看所有分支的所有操作记录（包括（包括commit和reset的操作），包括已经被删除的commit记录，git log则不能察看已经删除了的commit记录。

![这里写图片描述](https://img-blog.csdn.net/20161024172334515)

**上面红色箭头指的地方就查看不到记录。**
<!-- more -->
 4. （上面已经说明了,当前最新提交的分支在v2.2.2上）然后我们要恢复5d3c053 这个分支到v2.2.4上面，那我们就要使用**git cherry**了。
 5. 使用 cherry-pick.  根据git 文档：

```
Apply the changes introduced by some existing commits 
```

就是对已经存在的commit 进行apply (可以理解为再次提交）

简单用法：

```
git cherry-pick <commit id>
```


例如：


```
$  git checkout old_cc
$  git cherry-pick 67fbeee# 这个 67fbeee号码，位于：
```

```
Commit: 67fbeee108798cf43895db16cdcf91c2b1d5298d [67fbeee]
Parents: fa10c134b8
Author: xuhao <504105930@qq.com>
Date: 2016年10月21日 18:27:14
Labels: tag:, v2.2.3
更改版本号，解决发布问题标题问题

```

如上图：
由于adb install 这个记录可能在v2.2.2分支提交的，现在要把这个分支提交到v2.2.4分支上；

结果如下：

![这里写图片描述](https://img-blog.csdn.net/20161024175356461)，

最后之前丢失的那条记录也出来了，成功的解决了~~，sourceTree记录图如下：

![这里写图片描述](https://img-blog.csdn.net/20161024175647103)

总结：不管用什么工具，只有熟练掌握了命令才是王道啊~

> 本文由博主辛苦整理下来的笔记； 
> 希望大家能够指点或提出宝贵意见，共同学习，谢谢！ 
> 转载请注明出处：[http://xuhaoblog.com/git/not_push.html](http://xuhaoblog.com/git/not_push.html)
> 欢迎关注我的社交网站~
> 个人博客：[xuhaoblog.com](http://xuhaoblog.com)
> 新浪微博：[http://weibo.com/xuxiho](http://weibo.com/xuxiho)
> github：[https://github.com/git-xuhao](https://github.com/git-xuhao)

