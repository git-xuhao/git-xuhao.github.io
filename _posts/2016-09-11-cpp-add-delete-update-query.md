---
layout: post
title: C++ string字符串的增删改查
date: 2016-09-11 17:50:45
categories: C/C++
tags: C/C++
desc: c++ 提供的string类包含了若干实用的成员函数，大大方便了字符串的增加、删除、更改、查询等操作。
---
c++ 提供的string类包含了若干实用的成员函数，大大方便了字符串的增加、删除、更改、查询等操作。

#### 插入字符串

insert（）函数可以在string字符串中置顶的位置插入另一个字符串，它的原型为：

```
string& insert (size_t pos, const string& str);
```
看这个插入的格式我们就能猜想到，pos表示要插入的下标；str表示要插入的字符串，它可以是string变量，也可以是C风格的字符串。
<!-- more -->

看下面的代码：

```
#include <iostream>
#include <string>
using namespace std;
void main(){
	string s1, s2, s3;
	s1 = s2 = "1234567890";
	s3 = "aaa";
	s1.insert(5, s3);
	cout << s1 << endl;
	s2.insert(5, "bbb");
	cout << s2 << endl;
	

	system("pause");
}
```



运行结果：

```
12345aaa67890
12345bbb67890
请按任意键继续. . .

```

insert()函数的第一个参数有越界的可能，如果越界，则会产生运行时异常。我恶魔你要捕获这个异常。

---

#### **删除字符串**

erase()函数可以删除string变量中的一个字符串，原型为：

```
string& erase (size_t pos = 0, size_t len = npos);
```
pos 表示要删除的子字符串的起始下标，len表示要删除子字符串的长度。如果不指明len的话，那么直接删除pos到字符串结束处的所有字符（此时len =str.length-pos)。

示例代码如下：

```
#include <iostream>
#include <string>
using namespace std;
void main(){
    string s1, s2, s3;
    s1 = s2 = s3 = "1234567890";
    s2.erase(5);
    s3.erase(5, 3);
    cout<< s1 <<endl;
    cout<< s2 <<endl;
    cout<< s3 <<endl;
     system("pause");
}
```
运行结果：

```
1234567890
12345
1234590
请按任意键继续. . .
```

在 pos 参数没有越界的情况下， len 参数也可能会导致要删除的子字符串越界。但实际上这种情况不会发生，erase() 函数会从以下两个值中取出最小的一个作为待删除子字符串的长度：

* len的值
* 字符串长度减去 pos 的值。

简单的说，就是待删除字符串最多只能删除到字符串结尾。

---

#### **提取字符串**
substr()函数原型为：

```
string substr (size_t pos = 0, size_t len = npos) const;
```

pos为要提取的子字符串的起始下标，len为要提取的子字符串的长度。

```
#include <iostream>
#include <string>
using namespace std;
void  main(){
	string s1 = "first second third";
	string s2;
	s2 = s1.substr(6, 6);
	cout << s1 << endl;
	cout << s2 << endl;
	system("pause");
}
```

输出结果为：

```
first second third
second
请按任意键继续. . .

```
系统对 substr() 参数的处理和 erase() 类似：

* 如果 pos 越界，会抛出异常；
* 如果 len 越界，会提取从 pos 到字符串结尾处的所有字符。

---


#### **字符串的查找**

##### **find()函数**
find（）函数用于string字符串中查找子字符串出现的位置，它的原型为：

```
size_t find (const string& str, size_t pos = 0) const;
size_t find (const char* s, size_t pos = 0) const;
```

第一个参数的表示为待查找的子字符串，它可以是string变量，也可以是C风格的字符串，第二个参数表示开始查找的位置（下标）；

```
/字符串查找替换
void main()
{
	string s1 = "apple google apple iphone";
	//从0开始查找"google"的位置
	int idx = s1.find("google", 0);
	cout << idx << endl;

	//统计apple出现的次数
	int idx_app = s1.find("apple",0);
	//npos大于任何有效下标的值
	int num = 0;
	while (idx_app != string::npos)
	{
		num++;
		cout << "找到的索引:" << idx_app << endl;
		idx_app+=5;
		idx_app = s1.find("apple", idx_app);
	}

	cout << num << endl;
	system("pause");
}

```

输出结果为：

```
6
找到的索引:0
找到的索引:13
2
请按任意键继续. . .
```
find函数最终返回的是子字符串 第一次出现在字符串的其实下标，如果没有查找到子字符串，那么会返回一个无穷大的值 4294967295。统计apple出现的次数。先查找第一次出现的位置，接着
和npos大于任何有效下标的值，来判断，while循环，每次加上自身的长度，最后统计出现的次数。。。


---

##### **rfind()函数**

rfind() 和 find() 很类似，同样是在字符串中查找子字符串，不同的是 find() 函数从第二个参数开始往后查找，而 rfind() 函数则最多查找到第二个参数处，如果到了第二个参数所指定的下标还没有找到子字符串，则返回一个无穷大值4294967295。

```
#include <iostream>
#include <string>
using namespace std;
void main(){
	string s1 = "first second third";
	string s2 = "second";
	int index = s1.rfind(s2, 6);
	if (index < s1.length())
		cout << "Found at index : " << index << endl;
	else
		cout << "Not found" << endl;
	
	system("pause");
}
```

运行的结果为：

```
Found at index : 6
请按任意键继续. . .
```
---
##### **find_first_of() 函数**

find_first_of() 函数用于查找子字符串和字符串共同具有的字符在再辅传中首先出现的位置。

```
#include <iostream>
#include <string>
using namespace std;
int main(){
    string s1 = "first second second third";
    string s2 = "asecond";
    int index = s1.find_first_of(s2);
    if(index < s1.length())
        cout<<"Found at index : "<< index <<endl;
    else
        cout<<"Not found"<<endl;
    return 0;
}
```

运行结果为：

```
Found at index : 3
```
s1 和 s2 共同具有的字符是 ’s’，该字符在 s1 中首次出现的下标是3，故查找结果返回3。

> 学习理解并整理下来的笔记。  
> 希望大家能够指点或提出宝贵意见，谢谢！一起学习。 
> 转载请注明出处：
[http://blog.csdn.net/u011974987/article/details/52505004](http://blog.csdn.net/u011974987/article/details/52505004)
> 个人主页：[xuhaoblog.com](http://xuhaoblog.com)

 
 

