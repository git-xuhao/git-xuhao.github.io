---
layout: post
title: C++ string类和字符串的访问和拼接操作
date: 2016-09-11 14:28:31
category: C/C++
tags: C/C++
desc: C++ 增强了对字符串的支持，除了可以使用c中的字符串，还可以使用内置的数据类型string，string类处理字符串会翻遍很多，完全可以代替C语言中的char 数组和char 指针。
---

C++ 增强了对字符串的支持，除了可以使用c中的字符串，还可以使用内置的数据类型string，string类处理字符串会翻遍很多，完全可以代替C语言中的char 数组和char 指针。

使用sting类需要包含头文件< sting >, 下面我们就来介绍怎么使用。

**string 的几种用法：**

```
#include <iostream>
#include <string>

using namespace std;

void main(){

	string s1;
	string s2 = "c plus plus";
	string s3 = s2;
	string s4(5, 's');

	cout << s1 << s2 << s3 << s4 << endl;

	system("pause");


}
```
输出结果：

```
c plus plusc plus plussssss
请按任意键继续. . .
```
<!-- more -->

上面的几行代码介绍了 几种定义string 类型变量的方法，变量s1只是定义但是没有初始化，编译器会将默认的值附给s1，默认值是""（空字符串）。
变量s2再定义的同时被初始化为"c plus plus ”与C语言中的char 字符串不同，string类型的变量结尾没有
'\0',string 类型的本质是一个个string类，而我们定义的变量则是一个个的string类的对象，变量s3在定义的时候直接用s2进行初始化，因此s3的内容也是‘c plus plus'。变量s4被初始化为由5个's'字符串组成的字符串，也就是’sssss‘。

---
与C语言中的字符串不同，当我们需要知道字符串长度时，可以调用string类提供的length（）函数。如下所示：

```
string s = "c plus plus";
int len = s.length();
cout<<len<<endl;
```
编译运行的结果：

11

这里，变量 s 也是 string 类的对象，length() 是它的成员函数。由于 string 变量的末尾没有 '\0' 字符，所以 length() 返回的是字符串的真实长度，而不是长度 +1。
 
 ---
#### **转换为 char 数组字符串**
 C++虽然提供了string类来替代C语言中的char 数组形式的字符串，但是有时候编程中必须要使用C风格的字符串，为此，string类提供了一个转换的函数c_str();函数能够将 string 变量转换为一个 const 字符串数组的形式，并将指向该数组的指针返回。请看下面的代码：

```
string filename = "input.txt";
ifstream in;
in.open(filename.c_str());
```
为了使用文件打开函数 open()，必须将 string 类型的变量转换为字符串数组。

```
#ifndef _ITERATOR_DEBUG_LEVEL
#define _ITERATOR_DEBUG_LEVEL 0
#else
#undef _ITERATOR_DEBUG_LEVEL
#define _ITERATOR_DEBUG_LEVEL 0
#endif

#include <iostream>
#include <string>

//string字符串->c字符串转换
void main()
{
	//string -> char*
	string s1 = "far away";
	const char* c = s1.c_str();
	printf("%s\n",c);

	//
	string s2 = c;

	//string->char[]
	//从string中赋值字符到char[]
	char arr[50] = {0};
	s1.copy(arr,4,0);
	
	cout << arr << endl;

	system("pause");
}

```

编译输出结果：

```
far away
far
请按任意键继续. . .
```
---


#### **string 字符串的输入输出**

string类重载了输入输出的运算符，用“>>”进行输入，用"<<"进行输出，请看下面代码：

```
#include <iostream>
#include <string>
using namespace std;
int main(){
	string s;
	cin >> s;  //输入字符串
	cout << s << endl;  //输出字符串

	system("pause");
	return 0;
}
```

运行结果：

```
c plus
c
请按任意键继续. . .

```
虽然我们输入了两个由空格隔开的"c plus',但是只输出了一个，这是因为输入的运算符“>>"默认会忽略空格，遇到空格就认为输入结束，所以最后输入的plus没有被存储到变量里面。

---


#### **访问字符串中的字符**

string 字符串也可以像字符串数组一样按照下标来访问其中的每一个字符。string 字符串的起始下标仍是从 0 开始。请看下面的代码：

```
#include <string>
using namespace std;

//string遍历
void main(){
	string s1 = "abcdefg";

	//1、数组方式

	cout << "数组方式:" << endl;
	for (int i = 0; i < s1.length(); i++){
		cout <<s1[i] << endl;

	}

	//2、迭代方式
	cout << "迭代方式:" << endl;
	for (string::iterator it = s1.begin(); it != s1.end(); it++){

		cout<<*it<< endl;

	}

	system("pause");
}
```
输出结果为：
 

```
数组方式:
a
b
c
d
e
f
g
迭代方式:
a
b
c
d
e
f
g
请按任意键继续. . .
```


---

#### **字符串的拼接**

有了string 类，我们可以使用”+“ 或者”+=“运算符来直接拼接字符串，非常方便，再也不需要使用C语言中的strcat(),strcopy(),malloc()等函数来拼接字符串了，再也不用担心空间不够溢出了。
下面来看列子：

```
//字符串拼接
void main()
{
	string s1 = "alan";
	string s2 = "xiho";

	//1.
	string s3 = s1 + s2;

	string s4 = " wahaha";

	//2.
	s3.append(s4);

	cout << s3 << endl;

	system("pause");
}
```


 输出结果为：
 

```
alanxiho wahaha
请按任意键继续. . .
```

 ---

> 学习理解并整理下来的笔记。  
> 希望大家能够指点或提出宝贵意见，谢谢！一起学习。 
> 转载请注明出处：
[http://blog.csdn.net/u011974987/article/details/52504486](http://blog.csdn.net/u011974987/article/details/52504486)
> 个人主页：[xuhaoblog.com](http://xuhaoblog.com)

 
 
 
 
 
 




 
 
 
 

 


 
 
 






 
 
 
 
 
 