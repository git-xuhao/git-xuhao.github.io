---
layout: post
title: 基于Hexo+GitHub Pages 搭建个人博客教程
permalink: hexo-github-pages
date: 2016-05-06 15:44:04
tags: GitHub Hexo
categories: GitHub
top: true
desc: 这是一篇有关如何使用Github Pages和Hexo搭建自己独立博客的详尽教程，里面介绍了如何使用和配置Hexo框架，如何将Hexo部署到自己的Github项目中，域名注册，以及域名的绑定等功能。
---

![这里写图片描述](https://img-blog.csdn.net/20160506165509923)

摘要 :
----

这是一篇有关如何使用Github Pages和Hexo搭建自己独立博客的详尽教程，里面介绍了如何使用和配置Hexo框架，如何将Hexo部署到自己的Github项目中，域名注册，以及域名的绑定等功能。

前言：
---

就在一个多月前，我经过百般折腾花了三天时间搭建了一个属于自己的博客，我觉得还是很有必要把整个过程写下来，作为以后的备用，并且还能分享出来供大家学习使用，作为一个程序猿，或者希望在程序猿的道路上有所发展的骚年们，相信都应该有一个属于自己的技术博客吧！至于为什么，自己去体会；废话不多说，直奔主题~
<!-- more -->
### **一、系统环境配置**

要使用Hexo，需要在你的系统中支持Nodejs以及Git，如果还没有，那就开始安装吧！

#### **安装Node.js：**

[下载Node.js](https://nodejs.org/download/)
参考地址：[安装Node.js](http://www.w3cschool.cc/nodejs/nodejs-install-setup.html)
在 Windows 环境下安装 Node.js 非常简单，仅须到官网下载安装文件并执行即可完成安装。

![这里写图片描述](https://img-blog.csdn.net/20160505171021720)

#### **安装Git：**

* 下载地址：[点击下载Git](http://git-scm.com/download/)（这个地方可能要翻墙，如果你是windows 64位的，直接[点击这里](http://pan.baidu.com/s/1pLck50R)下载我分享的）

* 安装教程：[如何在windows下安装Git](http://jingyan.baidu.com/article/90895e0fb3495f64ed6b0b50.html)

* Git入门教程：[Pro Git（中文版）](http://git.oschina.net/progit/)

* Git基本操作：[Git常用的基本命令](http://blog.csdn.net/u011974987/article/details/50973740)


### **二、安装Hexo**
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

回到桌面点击右键，选择Bit Bash Here，如图：

![这里写图片描述](https://img-blog.csdn.net/20160505181859826)

然后输入一下安装hexo命令：

```
npm install -g hexo-cli
```
最后会出现大概这么一条信息 ![这里写图片描述](https://img-blog.csdn.net/20160506103224064)

说明hexo 已经安装成功了。（这个是针对Windows用户，Mac用户请参考[官方文档](https://hexo.io/zh-cn/docs/index.html)）

接下来，我们选择一个硬盘目录作为存放文件的路径，例如我手动在E 盘目录下建了一个blog的文件夹，然
后执行以下命令完成hexo 的初始化。

```
hexo init e:\blog
```
然后进入该目录：

```
cd e:\blog
```
执行以下命令，系统会可以根据package.json文件中dependencies的配置安装所有依赖包：

```
npm install
```
然后生成部署文件，启动本地服务

```
$ hexo s # 或者hexo server，可以在http://localhost:4000/ 查看

$ hexo g # 或者hexo generate

```
**Hexo** 几个常用的命令：

```
hexo generate (hexo g) 生成静态文件，会在当前目录下生成一个新的叫做public的文件夹
hexo server (hexo s) 启动本地web服务，用于博客的预览
hexo deploy (hexo d) 部署博客到远端服务器
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
```

可以简写成：

```
$ hexo n == hexo new
$ hexo g == hexo generate
$ hexo s == hexo server
$ hexo d == hexo deploy
```
都是这些单词的首字母，更多的命令用法，可以[参考Hexo官网](https://hexo.io/zh-cn/docs/commands.html)

现在我们打开[http://localhost:4000/](http://localhost:4000/) 就可以看到我们刚才搭建的本地博客了，Hexo会默认生成一个Hello World的博文。

以后每次新建文章或者修改，就可以先在本地预览，如果觉得没什么问题了之后再deploy到GitHub Pages上。接下来，就介绍GitPages是什么。

### **三、Github Pages设置**

#### **1、Github的优点**
GitHub是基于git实现的代码托管。git可能是目前最好用的版本控制系统了，非常受欢迎。
GitHub可以免费使用，并且快速稳定。
Github上面的世界很精彩，用久了你的眼界会开阔很多。
#### **2、什么是Github Pages?**
Github Pages可以被认为是用户编写的、托管在github上的静态网页。[GitHub Pages](https://pages.github.com/) 本用于介绍托管在GitHub的项目，不过，由于他的空间免费稳定，用来做搭建一个博客再好不过了。

![这里写图片描述](https://img-blog.csdn.net/20160505174712762)

#### **3、为什么要使用Github Pages**
可以绑定你的域名(但暂时貌似只能绑定一个)。
简单快捷，使用Github Pages可以为你提供一个免费的服务器，免去了自己搭建服务器和写数据库的麻烦.

#### **注册GitHub账号**

在创建博客之前，当然必须有GitHub的帐号，该帐号将用来创建项目，默认的域名username.github.com/projectName中的username也要用到这个帐号。

注意：下面涉及到的一些命令凡是更用户名和项目名有关的一律会用这里的username和projectName代替，注意替换
访问：http://www.github.com/

注册你的username和邮箱，邮箱十分重要，GitHub上很多通知都是通过邮箱的。比如你的主页上传并构建成功会通过邮箱通知，更重要的是，如果构建失败的话也会在邮件中说明原因。

#### **创建项目仓库**
在创建博客之前，还需要用已有的帐号创建一个项目，上面那个链接的projectName将是这里即将创建的项目名称。在Git中，项目被称为仓库(Repository)，仓库顾名思义，当然可以包含代码或者非代码。将来我们的网页或者模板实际上都是保存在这个仓库中的。

登录后，访问[https://github.com/new](https://github.com/new)，创建仓库如下图：

![这里写图片描述](https://img-blog.csdn.net/20160505175144095)

每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。你可以通过http://username.github.io 来访问你的个人主页。

创建了仓库后，我们就需要管理它，无论是管理本地仓库还是远程仓库都需要Git客户端；Git客户端实际上十分强大，它本身就可以offline的创建本地仓库，而本地仓库和远程仓库之间的同步也是通过Git客户端完成的。

#### **配置SSH**
上传文件需要配置ssh key，不然无法上传。
首先先检查一下本地是否已经存在ssh key,在Git Bash输入以下指令（任意位置点击鼠标右键），检查是否已经存在了SSH keys。
```
ls -al ~/.ssh
```
如果不存在就没有关系，如果存在的话，直接删除.ssh文件夹里面所有文件：
![这里写图片描述](https://img-blog.csdn.net/20160506112426664)

设置name和emai
```
git config --global user.name "<your name>"
git config --global user.email "<your email>"
```
需要注意的是这里的name是随意的，邮箱是你的联系邮箱，与github上的邮箱没有什么联系（不过我都是同一个邮箱）。

#### **生成ssh 密钥**
输入以下指令（邮箱就是你注册Github时候的邮箱）后，回车：

```
ssh-keygen -t rsa -C "XXXXX@qq.com"
```

一路按回车键即可，如果设置了密码请记住。
这一步在~/.ssh/下生成了两个文件id_rsa 和 id_rsa.pub

#### **获取Key**

```
$ cat ~/.ssh/id_rsa.pub
```
然后拷贝key

#### **在Github上添加SSH密钥**
在[https://github.com/settings/keys](https://github.com/settings/keys)下 add new ssh key
![这里写图片描述](https://img-blog.csdn.net/20160506113215698)

然后title随便取个名字，key 就是上面我们拷贝的内容，
好了，最后我们测试一下看是否配置成功。

输入以下命令：

```
ssh git@github.com
```

成功的话会显示以下的大致内容：

```
The authenticity of host 'github.com (192.30.252.128)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.252.128' (RSA) to the list of known hosts.
Hi git-xuhao! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

### **四、部署到GitHub**

#### **配置_config.yml**
* **编辑E:\hexo 下的_config.yml，修改 Deployment部分**
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
# ssh://git@github.com/git-xuhao/git-xuhao.github.io
deploy:
  type: git
  repo: ssh://git@github.com/git-xuhao/git-xuhao.github.io
  branch: master
```
其中，repo是我们刚刚建立的远程仓库，换成你自己的仓库，同时因为刚才配置了SSH-Key，所以必须是SSH形式的URL。值得注意的是，每一个: 后面都必须有一个空格，否则会引起错误。

* **安装Git包，执行以下命令**

```
npm install hexo-deployer-git --save
```

* **部署到GitHub上，执行以下的命令**

```
hexo deploy
```
现在我们可以通过访问 [http://git-xuhao.github.io/](http://git-xuhao.github.io/) 来访问我们自己的博客啦,可以看到我们的博客已成功搭建，接下来就是一些主题的优化和配置呢。

### **五、Hexo配置文件**

hexo里面有两个常用到的配置文件，分别是整个博客的配置文件E:\blog\_config.yml和主题的配置文件E:\hexo\themes\next\_config.yml，hexo3.0使用的默认主题是landscape，所以你们对应的地址为E:\hexo\themes\landscape\_config.yml；hexo提供了很多个主题供你自己选择，使用方法很简单，找到自己喜欢主题Repository，使用Git将主题clone到本地，然后将整个文件夹复制到E:\blog\themes文件夹下，将E"\blog\_config.yml里的theme名字改为自己下载的主题的文件夹名就可以了。

**配置文件大致说明如下：**

E:\blog\_config.yml

```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site	这下面的几项配置都很简单，你看我的博客就知道分别是什么意思
title: blog name	#博客名
subtitle: Goals determine what you are going to be	#副标题
description: Goals determine what you are going to be #用于搜索，没有直观表现
author: huangjunhui	#作者
language: zh-CN	#语言
timezone: 	#时区，此处不填写，hexo会以你目前电脑的时区为默认值

# URL	暂不配置，使用默认值
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory		暂不配置，使用默认值
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing	文章布局等，使用默认值
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  tab_replace:

# Category & Tag	暂不配置，使用默认值
default_category: uncategorized
category_map:
tag_map:

# Date / Time format	时间格式，使用默认值
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination	
## Set per_page to 0 to disable pagination
per_page: 10	#每页显示的文章数，0表示不分页
pagination_dir: page

# Extensions	插件配置，暂时不配置
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
plugins:
- hexo-generator-feed
theme: light	#使用的主题

feed:	#之后配置rss会用，此处先不配置这个
  type: atom
  path: atom.xml
  limit: 20  

# Deployment	用于部署到github，之前已经配置过
## Docs: http://hexo.io/docs/deployment.html

deploy:
  type: git
  repo: ssh://git@github.com/git-xuhao/git-xuhao.github.io
  branch: master
```

### **六、如何使用？**
新建一篇博文可通过以下的命令

```
hexo new "name"
```

其中name为博文的名字，建立完成之后，可以在./source/_posts文件夹下发现我们刚刚建立的 name.md文件。使用你熟悉的编辑器打开，便可以进行博文的撰写。博文支持MarkDown语法的编写，下面是一个示例文件的内容

```
---
title: name 
date: 2016-04-06 10:34:21
permalink: （url中显示的标题）
tags: 
- 开始
- 日志
categories: 
- 日志
---
Hello world，Test！！
```

博文写好之后，在每次发布之前，我们要先将写好的博客生成静态文件，执行以下命令

```
hexo g
```

之后生成的文件会放在./public目录下，这便是我们将要部署到GitHub上的全部内容。

静态文件生成之后，便可以部署到GitHub上

```
hexo d
```

然后打开我们的博客 [http://git-xuhao.github.io/](http://git-xuhao.github.io/) 已经可以看到刚刚新建的博文。
### **七、主题推荐**
Hexo提供了许多的主题可供我们选择和使用，在./themes目录下存放主题。刚才默认生成的博客用的就是默认的主题landscape，[hexo全部主题](https://github.com/hexojs/hexo/wiki/Themes)。
**知乎上的主题推荐：**[有哪些好看的 Hexo 主题？](https://www.zhihu.com/question/24422335)（对应的主题一般都有相关的使用说明，这里就不多说咯）
我自己使用的是**[Next](https://github.com/iissnan/hexo-theme-next)**主题，[xuhao.tech](http://xuhao.tech) Next主题又有三种风格，这个自己去体验研究吧！

### **八丶设置专属域名**
博客搭建好后，我们可以通过之前设置好的GitHub仓库地址来访问，比如我的：[http://git-xuhao.github.io](http://git-xuhao.github.io/)，而且GitHub是免费替我们托管的的，如果我们想要设置自己的专属的域名，我们可以去[阿里云](https://netcn.console.aliyun.com/core/domain/list)购买域名，我们点击添加记录，设置主机记录为@，类型为A，到IP 192.30.252.153（固定值）。

**如下图：**

![这里写图片描述](https://img-blog.csdn.net/20160506140554612)

按照如上设置完成之后， 可能不会立即生效，等个几分钟，在./source目录下新建文件CNAME（没有后缀名），文件中写上我们要绑定的域名，例如: xuhao.tech，然后执行

```
hexo g
```

生成静态文件，再执行

```
hexo d
```

部署到GitHub上。这时就可以通过[http://xuhaoblog.com](http://xuhaoblog.com)访问我们的博客了，怎么样？是不是很简单~不过在搭建的过程中可能遇到一些困难，没关系，耐心的查找问题所在，一切都能解决的。快动手试试吧！

**总结**
--
以上就是我采用Hexo+GitHub搭建个人博客的全部流程，但是有些细节性的问题可能表述的不是很明确，在搭建的过程中可能会遇到各种各样的问题，遇到问题，大家可以留言 我尽力所为，或者自行网上查找解决方案，Hexo主题才能大量的前端知识，毕竟我不是搞前端的，对这些知识也是了解很少，为了拥有一个自己的个人网站，也是拼了，最为一个程序员，那肯定是能提高自己的逼格的！哈哈~

**原文出处**
----

> 学习理解并整理下来的笔记；  
> 希望大家能够指点或提出宝贵意见，谢谢！一起学习；
> 转载请注明出处：[http://blog.csdn.net/u011974987/article/details/51331822](http://blog.csdn.net/u011974987/article/details/51331822)
> 个人主页：[xuhaoblog.com](http://xuhaoblog.com)















