---
layout: post
title: C 语言结构体和联合体（六）
permalink: c/structure-union
date: 2016-08-25 0:18:26
categories: C/C++
tags: C/C++
desc: 
---

### **结构体**


在C语言中，可以使用结构体（Struct）来存放一组不同类型的数据。结构体的定义形式为：

```
struct 结构体名{
    结构体所包含的变量或数组
};
```
结构体是一种集合，它里面包含了多个变量或数组，它们的类型可以相同，也可以不同，每个这样的变量或数组都称为结构体的成员（Member）。请看下面的一个栗子：

```
struct stu{
    char *name;  //姓名
    int num;  //学号
    int age;  //年龄
    char group;  //所在学习小组
    float score;  //成绩
};
```
<!-- more -->
stu 为结构体名，它包含了 5 个成员，分别是 name、num、age、group、score。结构体成员的定义方式与变量和数组的定义方式相同，只是不能初始化。

> 注意大括号后面的分号“；”不能少哦~

结构体也是一种数据类型，它由我们自己来定义，可以包含多个其他类型的数据。
像int、float、char 等是由C语言本身提供的数据类型，不能再进行分拆，我们称之为基本数据类型；而结构体可以包含多个基本类型的数据，也可以包含其他的结构体。

#### **结构体变量**

既然结构体是一种数据类型，那么就可以用它来定义变量。例如：

```
struct stu stu1, stu2;
```
定义了两个变量 stu1 和 stu2，它们都是 stu 类型，都由 5 个成员组成。注意关键字struct不能少。

还可以在定义结构体的同时定义结构体变量：

```
struct stu{
    char *name;  //姓名
    int num;  //学号
    int age;  //年龄
    char group;  //所在学习小组
    float score;  //成绩
} stu1, stu2;
```

如果只需要 stu1、stu2 两个变量，后面不需要再使用结构体名定义其他变量，那么在定义时也可以不给出结构体名，如下所示：

```
struct{  //没有写 stu
    char *name;  //姓名
    int num;  //学号
    int age;  //年龄
    char group;  //所在学习小组
    float score;  //成绩
} stu1, stu2;
```

这样的写法很简单，但是因为没有结构体名，后面就没法用该结构体定义新的变量了。

理论上讲结构体的各个成员在内存中是连续存储的，和数组非常类似，例如上面的结构体变量 stu1、stu2 的内存分布如下图所示，共占用 4+4+4+1+4 = 17 个字节。但是在编译器的具体实现中，各个成员之间可能会存在空隙，C语言中，结构体大小的内存分配，参考于这片文章：[C语言中结构体大小计算即存储分配](http://blog.csdn.net/zhangjay/article/details/6408907)

这里我在做下总结：

   运算符sizeof可以计算出给定类型的大小，对于32位系统来说，sizeof(char) = 1; sizeof(int) = 4。基本数据类型的大小很好计算，我们来看一下如何计算构造数据类型的大小。

　　C语言中的构造数据类型有三种：数组、结构体和共用体。

　　数组是相同类型的元素的集合，只要会计算单个元素的大小，整个数组所占空间等于基础元素大小乘上元素的个数。

　　结构体中的成员可以是不同的数据类型，成员按照定义时的顺序依次存储在连续的内存空间。和数组不一样的是，结构体的大小不是所有成员大小简单的相加，需要考虑到系统在存储结构体变量时的地址对齐问题。看下面这样的一个结构体：

```
　　struct stu1
　　{
　　int i;
　　char c;
　　int j;
　　}；
```

　　先介绍一个相关的概念——偏移量。偏移量指的是结构体变量中成员的地址和结构体变量地址 的差。结构体大小等于最后一个成员的偏移量加上最后一个成员的大小。显然，结构体变量中第一个成员的地址就是结构体变量的首地址。因此，第一个成员i的偏 移量为0。第二个成员c的偏移量是第一个成员的偏移量加上第一个成员的大小（0+4）,其值为4；第三个成员j的偏移量是第二个成员的偏移量加上第二个成 员的大小（4+1）,其值为5。

　　实际上，由于存储变量时地址对齐的要求，编译器在编译程序时会遵循两条原则：一、结构体变量中成员的偏移量必须是成员大小的整数倍（0被认为是任何数的整数倍） 二、结构体大小必须是所有成员大小的整数倍。

　　对照第一条，上面的例子中前两个成员的偏移量都满足要求，但第三个成员的偏移量为5，并不是自身(int)大小的整数倍。编译器在处理时会在第二个成员后面补上3个空字节，使得第三个成员的偏移量变成8。

　　对照第二条，结构体大小等于最后一个成员的偏移量加上其大小，上面的例子中计算出来的大小为12，满足要求。

　　再看一个满足第一条，不满足第二条的情况：

```
　　struct stu2
　　{
　　int k;
　　short t;
　　}；
```

　　成员k的偏移量为0；成员t的偏移量为4，都不需要调整。但计算出来的大小为6，显然不 是成员k大小的整数倍。因此，编译器会在成员t后面补上2个字节，使得结构体的大小变成8从而满足第二个要求。由此可见，大家在定义结构体类型时需要考虑 到字节对齐的情况，不同的顺序会影响到结构体的大小。对比下面两种定义顺序

```
　　struct stu3
　　{
　　char c1;
　　int i;
　　char c2;
　　}

　　struct stu4
　　{
　　char c1;
　　char c2;
　　int i;
　　}
```

　　虽然结构体stu3和stu4中成员都一样，但sizeof(struct stu3)的值为12而sizeof(struct stu4)的值为8。

　　如果结构体中的成员又是另外一种结构体类型时应该怎么计算呢？只需把其展开即可。但有一点需要注意，展开后的结构体的第一个成员的偏移量应当是被展开的结构体中最大的成员的整数倍。看下面的例子：

```
　　struct stu5
　　{
　　short i;
　　
　　struct{
　　  char c;
　　  int j;
　　} ss;
　
　　int k;
　　}
```

　　结构体stu5的成员ss.c的偏移量应该是4，而不是2。整个结构体大小应该是16。

　　如何给结构体变量分配空间由编译器决定，以上情况针对的是Linux下的GCC。其他平台的C编译器可能会有不同的处理，看到这里估计还是有些同学不太明白，多看几遍，领悟领悟，就好啦！

#### **成员的获取和赋值**

结构体和数组类似，也是一组数据的集合，整体使用没有太大的意义。数组使用下标[ ]获取单个元素，结构体使用点号.获取单个成员。获取结构体成员的一般格式为：

```
结构体变量名.成员名;
```
通过上面的格式就可以获取成员的值，和给成员赋值，看下面的栗子：

```
#include <stdio.h>
int main(){
    struct{
        char *name;  //姓名
        int num;  //学号
        int age;  //年龄
        char group;  //所在小组
        float score;  //成绩
    } stu1;
    //给结构体成员赋值
    stu1.name = "haozi";
    stu1.num = 12;
    stu1.age = 18;
    stu1.group = 'A';
    stu1.score = 136.5;
    //读取结构体成员的值
    printf("%s的学号是%d，年龄是%d，在%c组，今年的成绩是%.1f！\n", stu1.name, stu1.num, stu1.age, stu1.group, stu1.score);
    return 0;
}
```
**运行结果：**
haozi的学号是12，年龄是18，在A组，今年的成绩是136.5！
除了这种方式赋值外，还可以在定义的时候赋值：

```
struct{
    char *name;  //姓名
    int num;  //学号
    int age;  //年龄
    char group;  //所在小组
    float score;  //成绩
} stu1, stu2 = { "haozi", 12, 18, 'A', 136.5 };
```
不过整体赋值仅限于定义结构体变量的时候，在使用过程中只能对成员逐一赋值，这和数组的赋值非常类似。

#### **结构体与指针**
指针也可以指向一个结构体，定义的形式一般为：

```
struct 结构体名 *变量名;
```



看例子：

```
struct Man{
	char name[20];
	int age;
};
void main(){
	struct Man m1 = {"Jack",30};
	//结构体指针
	struct Man *p = &m1;
	printf("%s,%d\n", m1.name, m1.age);
	printf("%s,%d\n",(*p).name,(*p).age);
	//“->”（箭头）是“(*p).”简写形式
	printf("%s,%d\n", p->name, p->age);
	//(*env)->

	system("pause");
}
```

编译出的结果是：

```
Jack,30
Jack,30
Jack,30
请按任意键继续. . .
```

  上面代码：`printf("%s,%d\n", m1.name, m1.age);`还可以换成： `printf("%s,%d\n", （*p）.name, m1.age);`或者 `printf("%s,%d\n", p->name, m1.age)`;
其运行结果还是一样的。

获取结构体成员通过结构体指针可以获取结构体成员，一般形式为：

```
(*pointer).memberName
```

或者：

```
pointer->memberName
```

### **联合体（共用体）**

结构体（Struct）是一种构造类型或复杂类型，它可以包含多个类型不同的成员。在C语言中，还有另外一种和结构体非常类似的语法，叫做共用体（Union），它的定义格式为：

```
union 共用体名{
    成员列表
};
```
共用体有时也被成为联合体；

结构体和共用体的区别在于：结构体的各个成员会占用不同的内存，互相之间没有影响；而共用体的所有成员占用同一段内存，修改一个成员会影响其余所有成员。

结构体占用的内存大于等于所有成员占用的内存的总和（成员之间可能会存在缝隙），共用体占用的内存等于最长的成员占用的内存。共用体使用了内存覆盖技术，同一时刻只能保存一个成员的值，如果对新的成员赋值，就会把原来成员的值覆盖掉。

共用体也是一种自定义类型，可以通过它来创建变量，例如：

```
union data{
    int n;
    char ch;
    double f;
};
union data a, b, c;
```
上面是先定义共用体，再创建变量，也可以在定义共用体的同时创建变量：

```
union data{
    int n;
    char ch;
    double f;
} a, b, c;
```

共用体 data 中，成员 f 占用的内存最多，为 8 个字节，所以 data 类型的变量（也就是 a、b、c）也占用 8 个字节的内存，请看下面的栗子：

```
#include<stdio.h>  
union var{  
        long j;  
        int i;  
};  
main(){  
        union var v;  
        v.j = 5;  
        printf("v.j is %d\n",v.i);  
        v.i = 6;  //最后一次赋值有效
        printf("now v.j is %ld! the address is %p\n",v.j,&v.j);  
        
        printf("now v.i is %d! the address is %p\n",v.i,&v.i);  

		system("pause");
}  
```
编译并运行结果：

```
v.j is 5  
now v.j is 6! the address is 0xbfad1e2c  
now v.i is 6! the address is 0xbfad1e2c  
```
这段代码不但验证了共用体的长度，还说明共用体成员之间会相互影响，修改一个成员的值会影响其他成员。


# CSDN
### 更多系列相关文章传送门：


- [C语言（一）基本数据类型](http://blog.csdn.net/u011974987/article/details/52138798) 
- [C语言（二）有符号数和无符号数的理解](http://blog.csdn.net/u011974987/article/details/52142794) 
- [C语言（三）字符串处理函数](http://blog.csdn.net/u011974987/article/details/52200178)
- [C语言（四）指针概念的理解](http://blog.csdn.net/u011974987/article/details/52270018)
- [C语言（五）内存的分配与释放](http://blog.csdn.net/u011974987/article/details/52290724)
- [C语言（六）结构体和联合体](http://blog.csdn.net/u011974987/article/details/52305364)
- [C语言（七）文件的相关操作](http://blog.csdn.net/u011974987/article/details/52354074)
