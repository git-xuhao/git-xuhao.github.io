---
layout: post
title: C 语言的基本数据类型（一）
permalink: c-language
date: 2016-08-07 07:00:33
categories: C/C++
tags: C/C++
---

## C语言helloworld
---
1.声明头文件，相当于java的导包。
	

```
   //只有函数的声明，编译时会去找到函数的实现
	#include <stdio.h> 
```
<!-- more -->

2.写main()方法 
	

```
	main(){
		 printf("hello from c\n");    //\n换行   
	}
```

3.为了观察程序执行的结果，需要调用windows系统的一个指令

```
	//等待输入
	system("pause"); 或者getchar();
```

## C语言的基本数据类型
---
* **C语言的数据类型：** 

 ![这里写图片描述](http://img.blog.csdn.net/20160806212417802)

* **java语言的8大基本类型** 
	- int   4byte  32位
	- byte  1byte  8位
	- short 2byte  16位
	- long  8byte   64位
	- float 4byte   32位
	- double 8byte  64位
	- boolean 1byte 8位
	- char   2byte 16位

* **c语言的基本数据类型**
	 > sizeof();获取一个数据类型的长度 
	
	- char    1byte 8位 和java不同
	- long    4byte 32位和java不同
	- int       4byte 32位 和java一致
	- float    4byte 32位 和java一致
	- double     8byte  64位和java一致
	- short 2byte      16位 还可以表示java里面的char
	- 在c语言里面没有boolean类型，0假 非0真
	- 在c语言里面没有byte类型 可以用char表示byte类型。


代码如下：

```
/**
char, int, float, double, long, short and  组合关键字signed, unsigned,  void
sizeof();获取一个数据类型的长度 
**/
#include <stdio.h>
main(){
        
    printf("char的占据的byte为%d \n",sizeof(char));    
    printf("int的占据的byte为%d \n",sizeof(int));    
    printf("float的占据的byte为%d \n",sizeof(float));    
    printf("double的占据的byte为%d \n",sizeof(double));    
    printf("long的占据的byte为%d \n",sizeof(long));    
    printf("short的占据的byte为%d \n",sizeof(short));    
  
    printf("signed int的占据的byte为%d \n",sizeof(signed int));      // -2的31次方~ 2的31次方-1 
    printf("unsigned int的占据的byte为%d \n",sizeof(unsigned int));    //0~ 2的32次方-1  
   
    printf("int*占据的byte为%d \n " ,sizeof(int*)); 
    printf("char*占据的byte为%d \n " ,sizeof(char*)); 
    printf("float*占据的byte为%d \n " ,sizeof(float*)); 
    printf("double*占据的byte为%d \n " ,sizeof(double*)); 
    printf("long*占据的byte为%d \n " ,sizeof(long*)); 
    printf("short*占据的byte为%d \n " ,sizeof(short*)); 

  
    system("pause");
     
}

```
运行结果如下：
![这里写图片描述](http://img.blog.csdn.net/20160806213113253)

CSDN
更多系列相关文章传送门：
-------

[C语言（一）基本数据类型](http://blog.csdn.net/u011974987/article/details/52138798) 
[C语言（二）有符号数和无符号数的理解](http://blog.csdn.net/u011974987/article/details/52142794) 
[C语言（三）字符串处理函数](http://blog.csdn.net/u011974987/article/details/52200178)
[C语言（四）指针概念的理解](http://blog.csdn.net/u011974987/article/details/52270018)
[C语言（五）内存的分配与释放](http://blog.csdn.net/u011974987/article/details/52290724)
[C语言（六）结构体和联合体](http://blog.csdn.net/u011974987/article/details/52305364)
[C语言（七）文件的相关操作](http://blog.csdn.net/u011974987/article/details/52354074)

> 学习理解并整理下来的笔记。  
> 希望大家能够指点或提出宝贵意见，谢谢！一起学习。 
> 个人主页：[xuhaoblog.com](http://xuhaoblog.com)

