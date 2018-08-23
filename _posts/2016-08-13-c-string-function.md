---
layout: post
title: C 语言字符串处理函数（三）
date: 2016-08-13 19:30:00
categories: C/C++
tags: C/C++
desc: C语言提供了丰富的字符串处理函数，例如字符串的输入、输出、合并、修改、比较、转换、复制、搜索等，使用这些现成的函数可大大减轻编程的负担。
---



C语言提供了丰富的字符串处理函数，例如字符串的输入、输出、合并、修改、比较、转换、复制、搜索等，使用这些现成的函数可大大减轻编程的负担。

用于输入输出的字符串函数，例如printf、puts、scanf、gets等，使用时应包含头文件stdio.h，使用其它字符串函数则应包含头文件string.h。

<!-- more -->

### **1.字符串长度函数strlen**
strlen 是 string length 的缩写，用来获得字符串的长度。所谓长度，就是包含多少个字符（不包括字符串结束标志 '\0'）。语法格式为：

```
strlen(arrayName);
```

strlen 将返回字符串的长度，它是一个整数。请看下面的例子：

```
#include <stdio.h>
#include <string.h>
int main(){
    char str[]="C language";
    int len = strlen(str);
    printf("The lenth of the string is %d\n", len);
    return 0;
}
```

运行结果：

```
The lenth of the string is 10
```

需要说明的是，strlen 会从字符串的第 0 个字符开始计算，直到遇到字符串结束标志 '\0'。将上面代码中的 str 改为：

```
char str[]="C \0language";
```

那么输出结果就是：
**The lenth of the string is 2**

### **2.字符串连接函数 strcat**

strcat 是 string catenate 的缩写，意思是把两个字符串拼接在一起，语法格式为：

```
strcat(arrayName1, arrayName2);
```

arrayName1、arrayName2 为需要拼接的字符串。

strcat 将把 arrayName2 连接到 arrayName1 后面，并删去 arrayName1 最后的结束标志 '\0'。
**这就意味着，arrayName1 的长度要足够，必须能够同时容纳 arrayName1 和 arrayName2，否则会越界。**

strcat 返回值为 arrayName1 的首地址。请看下面的例子：

```
#include <stdio.h>
#include <string.h>
int main(){
    char str1[40]="My name is ";
    char str2[20];
    printf("Input your name:");
    gets(str2);
    strcat(str1,str2);
    puts(str1);
    return 0;
}
```

运行结果：

```
Input your name: xuhao
My name is xuhao
```

### **3.字符串复制函数strcpy**

strcpy 是 string copy 的缩写，意思是字符串复制，语法格式为：

```
strcpy(arrayName1, arrayName2);
```

strcpy 会把 arrayName2 中的字符串拷贝到 arrayName1 中，串结束标志 '\0' 也一同拷贝。请看下面的例子：

```
#include <stdio.h>
#include <string.h>
int main(){
    char str1[15], str2[]="C Language";
    strcpy(str1, str2);
    puts(str1);
    printf("\n");
    return 0;
}
```

运行结果：

**C Language**

strcat 要求 arrayName1 要有足够的长度，否则不能全部装入所拷贝的字符串。


### **4.字符串比较函数strcmp**

strcmp 是 string compare 的缩写，意思是字符串比较，语法格式为：

```
strcmp(arrayName1, arrayName2);
```

arrayName1 和 arrayName2 是需要比较的两个字符串。

字符本身没有大小之分，strcmp() 是以各个字符在ASCII 码表上对应的数值进行比较的。strcmp() 首先将 arrayName1 中第0个字符的 ASCII 码值减去 arrayName2 中第0个字符的 ASCII 码值，若差值为 0，则说明两个字符相同，再继续比较下个字符，若差值不为 0 则将差值返回。例如字符串"Ac"和"ba"比较则会返回字符"A"(65)和'b'(98)的差值(－33)。

返回值：若 arrayName1 和 arrayName2 相同，则返回0；若 arrayName1 大于 arrayName2，则返回大于 0 的值；若 arrayName1 小于 arrayName2，则返回小于0 的值。

下面对4组字符串进行比较。

```
#include <string.h>
main(){
	char *a = "AaBbCc";
	char *b = "AbCdEf";
	char *c = "aacdef";
	char *d = "AaBbCc";
	printf("strcmp(a, b) : %d\n", strcmp(a, b));
	printf("strcmp(a, c) : %d\n", strcmp(a, c));
	printf("strcmp(a, d) : %d\n", strcmp(a, d));


	getchar();
}
```

运行结果：

```
strcmp(a, b) : -1
strcmp(a, c) : -1
strcmp(a, d) : 0
```


# CSDN
### 更多系列相关文章传送门：


- [C语言（一）基本数据类型](http://blog.csdn.net/u011974987/article/details/52138798) 
- [C语言（二）有符号数和无符号数的理解](http://blog.csdn.net/u011974987/article/details/52142794) 
- [C语言（三）字符串处理函数](http://blog.csdn.net/u011974987/article/details/52200178)
- [C语言（四）指针概念的理解](http://blog.csdn.net/u011974987/article/details/52270018)
- [C语言（五）内存的分配与释放](http://blog.csdn.net/u011974987/article/details/52290724)
- [C语言（六）结构体和联合体](http://blog.csdn.net/u011974987/article/details/52305364)
- [C语言（七）文件的相关操作](http://blog.csdn.net/u011974987/article/details/52354074)

