---
layout: post
title: C 语言文件的相关操作（七）
permalink: c/c-file-operation 
date: 2016-08-29 17:12:26
categories: C/C++
tags: C/C++
desc: 在C语言中，文件操作都是由库函数来完成的，我们就来总结文件的相关的操作。
---

**C语言文件的打开与关闭**
-----------

----------
在C语言中，文件操作都是由库函数来完成的，我们就来总结文件的相关的操作。

### **文件的打开(fopen函数)**

fopen() 函数用来打开一个文件，它的格式为：

```
FILE *fopen(char *filename, char *type);
```

filename为文件名（包括文件路径），type为打开方式，它们都是字符串。fopen() 会获取文件信息，包括文件名、文件状态、当前读写位置等，并将这些信息保存到一个FILE类型的结构体变量中，然后将该变量的地址返回。

如果接收 fopen() 的返回值，就需要定义一个 FILE 类型的指针。例如：

```
FILE *fp = ("demo.txt", "r");
```

 <!-- more -->
上面是以“只读”方式打开当前目录下的 demo.txt 文件，并使 fp 指向该文件，这样就可以通过 fp 来操作 demo.txt 了。fp 通常被称为文件指针。又如：

```
FILE *fp = fopen("D:\\demo.txt","rb");
```

表示以二进制方式打开 D 盘下的 demo.txt 文件，允许读和写。

打开方式(mode)有多种：


----------

**使用文件方式	含义**
"r"（只读）	为输入打开一个文本文件
"w"（只写）	为输出打开一个文本文件
"a"（追加）	为追加打开一个文本文件
"rb"（只读）	为输入打开一个二进制文件
"wb"（只写）	为输出打开一个二进制文件
"ab"（追加）	为追加打开一个二进制文件
"r+"（读写）	为读／写打开一个文本文件
"w+"（读写）	为读／写创建一个文本文件
"a+"（读写）	为读／写打开一个文本文件
"rb+"（读写）	为读／写打开一个二进制文件
"wb+"（读写）	为读／写创建一个二进制文件
"ab+"（读写）	为读／写打开一个二进制文件


----------



在打开一个文件时，如果出错，fopen将返回一个空指针值NULL，因此，我们可以在程序中这样来处理

```
if( (fp=fopen("D:\\demo.txt","rb") == NULL ){
    printf("open file is error D:\\demo.txt file!");
    getchar();
    exit(1);
}
```

如果再打开文件是，返回的指针为空，则表示不能打开D盘跟目录下的demo.txt 文件，输出提示的错误信息！！printf("open file is error D:\\demo.txt file!");

### **文件关闭（fclose函数）**

fp 为文件指针。例如：

```
fclose(fp);
```

文件正常关闭时，fclose() 的返回值为0，如果返回非零值则表示有错误发生。

**C中文本文件和二进制文件的区别**
-----------

下面这段摘自网上：

从文件编码的方式来看，文件可分为ASCII码文件和二进制码文件两种。

　　ASCII文件也称为文本文件，这种文件在磁盘中存放时每个字符对应一个字节，用于存放对应的ASCII码。例如，数5678的存储形式为：
ASC码： 　00110101 00110110 00110111 00111000
　　　　　↓ 　　　　↓　　　　↓ 　　　↓
十进制码： 5　　　　　6　　　　7　　　　8 共占用4个字节。ASCII码文件可在屏幕上按字符显示， 例如源程序文件就是ASCII文件，用DOS命令TYPE可显示文件的内容。 由于是按字符显示，因此能读懂文件内容。

　　二进制文件是按二进制的编码方式来存放文件的。 例如， 数5678的存储形式为： 00010110 00101110只占二个字节。二进制文件虽然也可在屏幕上显示， 但其内容无法读懂。C系统在处理这些文件时，并不区分类型，都看成是字符流，按字节进行处理。 输入输出字符流的开始和结束只由程序控制而不受物理符号(如回车符)的控制。 因此也把这种文件称作“流式文件”。
一个文件可以以文本模式或二进制模式打开，这两种的区别是：在文本模式中回车被当成一个字符'/n'，而二进制模式认为它是两个字符0x0D,0x0A；如果在文件中读到0x1B，文本模式会认为这是文件结束符，也就是二进制模型不会对文件进行处理，而文本方式会按一定的方式对数据作相应的转换。

----------

**C语言以字符形式读写文件**
-----------

以字符形式读写文件时，每次可以从文件中读取一个字符，或者向文件中写入一个字符。主要使用两个函数：fgetc()和fputc()。

### **字符读取函数 fgetc**

fgetc 是 file get char 的缩写，意思是从指定的文件中读取一个字符。它的原型为：

```
int fgetc (FILE *fp);
```
fp 为文件指针。fgetc() 读取成功时返回读取到的字符，读取到文件末尾或读取失败时返回EOF。

EOF 是 end of file 的缩写，表示文件末尾，是在 stdio.h 中定义的宏，它的值是一个负数，往往是 -1。返回值类型之所以为 int，就是为了容纳这个负数（char不能是负数）。

> EOF 不绝对是 -1，也可以是其他负数，这要看编译器的实现。

fgetc() 使用举例：

```
char ch;
FILE *fp = fopen("D:\\demo.txt", "r+");
ch = fgetc(fp);
```

表示从D:\\demo.txt文件中读取一个字符，并保存到变量ch中。

在文件内部有一个位置指针，用来指向当前读写到的位置，也就是读写到第几个字节。在文件打开时，该指针总是指向文件的第一个字节。使用fgetc 函数后，该指针会向后移动一个字节，所以可以连续多次使用fgetc读取多个字符。


> **注意：**这个文件内部的位置指针与C语言中的指针不是一回事。位置指针仅仅是一个标志，表示文件读写到的位置，也就是读写到第几个字节，它不表示地址。文件每读写一次，位置指针就会移动一次，它不需要你在程序中定义和赋值，而是由系统自动设置，对用户是透明的。

实例：

```
#include<stdio.h>
int main(){
    FILE *fp;
    char ch;
   
    //如果文件不存在，给出提示并退出
    if( (fp=fopen("D:\\demo.txt","rt")) == NULL ){
        printf("Cannot open file, press any key to exit!");
        getch();
        exit(1);
    }
    //每次读取一个字节，直到读取完毕
    while( (ch=fgetc(fp)) != EOF ){
        putchar(ch);
    }
    putchar('\n');  //输出换行符
    fclose(fp);
    return 0;
}
```

在D盘下创建demo.txt文件，输入任意内容并保存，运行程序，就会看到刚才输入的内容全部都显示在屏幕上。

该程序的功能是从文件中逐个读取字符，在屏幕上显示，直到读取完毕。

程序第14行是关键，while 循环的条件为(ch=fgetc(fp)) != EOF。fget() 每次从位置指针所在的位置读取一个字符，并保存到变量 ch，位置指针向后移动一个字节。当文件指针移动到文件末尾时，fget() 就无法读取字符了，于是返回 EOF，表示文件读取结束了。

### **对EOF的说明**

EOF 本来表示文件末尾，意味着读取结束，但是很多函数在读取出错时也返回 EOF，那么当返回EOF时，到底是文件读取完毕了还是读取出错了？我们可以借助 stdio.h 中的两个函数来判断，分别是 feof() 和 ferror()。

feof() 函数用来判断文件内部指针是否指向了文件末尾，它的原型是：

```
int feof ( FILE * fp );
```

当指向文件末尾时返回非零值，否则返回零值。


ferror() 函数用来判断文件操作是否出错，它的原型是：

```
int ferror ( FILE *fp );
```

出错时返回非零值，否则返回零值。

需要说明的是，文件出错是非常少见的情况，上面的示例基本能够保证将文件内的数据读取完毕。如果追求完美，也可以加上判断并给出提示：


```
#include<stdio.h>
int main(){
    FILE *fp;
    char ch;
  
    //如果文件不存在，给出提示并退出
    if( (fp=fopen("D:\\demo.txt","rt")) == NULL ){
        printf("Cannot open file, press any key to exit!");
        getch();
        exit(1);
    }
    //每次读取一个字节，直到读取完毕
    while( (ch=fgetc(fp)) != EOF ){
        putchar(ch);
    }
    putchar('\n');  //输出换行符
    if(ferror(fp)){
        puts("读取出错");
    }else{
        puts("读取成功");
    }
    fclose(fp);
    return 0;
}
```

这样，不管是出错还是正常读取，都能够做到心中有数。

### 字符写入函数fputc

fputc 是 file output char 的所以，意思是向指定的文件中写入一个字符。调用的形式为：

```
int fputc ( int ch, FILE *fp );
```

ch 为要写入的字符，fp 为文件指针。fputc() 写入成功时返回写入的字符，失败时返回EOF，返回值类型为 int 也是为了容纳这个负数。例如：

```
fputc('a', fp);
```

或

```
char ch = 'a';
fputc(ch, fp);
```
表示把字符 'a' 写入fp所指向的文件中。

**两点说明：**

1) 被写入的文件可以用写、读写、追加方式打开，用写或读写方式打开一个已存在的文件时将清除原有的文件内容，并将写入的字符放在文件开头。如需保留原有文件内容，并把写入的字符放在文件末尾，就必须以追加方式打开文件。不管以何种方式打开，被写入的文件若不存在时则创建该文件。

2) 每写入一个字符，文件内部位置指针向后移动一个字节。

```
#include<stdio.h>
int main(){
    FILE *fp;
    char ch;
    //判断文件是否成功打开
    if( (fp=fopen("D:\\demo.txt","wt+")) == NULL ){
        printf("Cannot open file, press any key to exit!\n");
        getch();
        exit(1);
    }
    printf("Input a string:\n");
    //每次从键盘读取一个字符并写入文件
    while ( (ch=getchar()) != '\n' ){
        fputc(ch,fp);
    }
    fclose(fp);
    return 0;
}
```

运行程序，输入一行字符并按回车键结束，打开D盘下的demo.txt文件，就可以看到刚才输入的内容。

----------


**C语言以字符串形式读写文件**
-----------

上面一个模块fgetc()和fputc()函数每次只能读写一个字符，速度比较慢，在实际过程中是每次读写一个字符串或者一个数据块，这样能明显提高效率。

### **读字符串函数fgets**
fgets() 函数用来从指定的文件中读取一个字符串，并保存到字符数组中，它的原型为：

```
char *fgets ( char *str, int n, FILE *fp );
```

str 为字符数组，n 为要读取的字符数目，fp 为文件指针。
**返回值：**读取成功时返回字符数组首地址，也即 str；读取失败时返回 NULL；如果开始读取时文件内部指针已经指向了文件末尾，那么将读取不到任何字符，也返回 NULL。

读取到的字符串会在末尾自动添加 '\0'，n 个字符也包括 '\0'。也就是说，实际只读取到了 n-1 个字符，如果希望读取 100 个字符，n 的值应该为 101。例如：

```
#define N 101
char str[N];
FILE *fp = fopen("D:\\demo.txt", "r");
fgets(str, N, fp);
```

表示从 D:\\demo.txt 中读取100个字符，并保存到字符数组str中。

需要重点说明的是，在读取到 n-1 个字符之前如果出现了换行，或者读到了文件末尾，则读取结束。这就意味着，不管n的值多大，fgets() 最多只能读取一行数据，不能跨行。在C语言中，没有按行读取文件的函数，我们可以借助 fgets()，将n的值设置地足够大，每次就可以读取到一行数据。

一行一行的读取文件，代码如下：

```
#include <stdio.h>
#include <stdlib.h>
#define N 100
int main(){
    FILE *fp;
    char str[N+1];
    if( (fp=fopen("d:\\demo.txt","rt")) == NULL ){
        printf("Cannot open file, press any key to exit!\n");
        getch();
        exit(1);
    }
   
    while(fgets(str, N, fp) != NULL){
        printf("%s", str);
    }
    fclose(fp);
    system("pause");
    return 0;
}
```

在这个D:\\demo.txt：文件中写入下面的内容：

 My Name is Android
 Google

那么编译运行上面的程序，结果为：

```
My Name is Android 
Google 请按任意键继续...
```

### 写字符串函数fputs
fputs 函数是指想一个文件中写入一个字符串，它的原型为：

```
int  fputs(char *str,FILE *fp);
```

 str为要写入的字符串，fp为文件指针，写入成功返回非负数，失败返回EOF。
 eg：
 

```
char *str = "xuhao";
FILE *fp = fopen("D:\\demo.text","a+");
```
表示把字符串str写入到D：\\demo.txt文件中。

在上述代码中建立的D：\\demo.txt 文件中追加一个字符串。

```
#include<stdio.h>
int main(){
    FILE *fp;
    char str[102] = {0}, strTemp[100];
    if( (fp=fopen("D:\\demo.txt", "at+")) == NULL ){
        printf("Cannot open file, press any key to exit!\n");
        getch();
        exit(1);
    }
    printf("Input a string:");
    gets(strTemp);
    strcat(str, "\n");
    strcat(str, strTemp);
    fputs(str, fp);
    fclose(fp);
    return 0;
}
```
编译并运行程序，在弹出的视图中 输入java C++，打开 D:\\demo.txt，文件内容为：

```
My Name is Android 
Google 
java C++ 
```



**C语言中其他文件的操作**
-----------



比如文件复制，代码如下：
```
void main(){
	char *read_path = "E:\\dongnao\\vip\\ndk\\08_08_C_05\\files\\liuyan.png";
	char *write_path = "E:\\dongnao\\vip\\ndk\\08_08_C_05\\files\\liuyan_new.png";
	//读的文件 b字符表示操作二进制文件binary
	FILE *read_fp = fopen(read_path, "rb");
	//写的文件
	FILE *write_fp = fopen(write_path, "wb");
	
	//复制
	int buff[50]; //缓冲区域
	int len = 0; //每次读到的数据长度
	while ((len = fread(buff, sizeof(int), 50, read_fp)) != 0){
		//将读到的内容写入新的文件
		fwrite(buff,sizeof(int),len,write_fp);
	}
	//关闭流
	fclose(read_fp);
	fclose(write_fp);
	getchar();
}
```

```
//获取文件的大小
void main(){
	char *read_path = "E:\\dongnao\\vip\\ndk\\08_08_C_05\\files\\liuyan.png";
	FILE *fp = fopen(read_path, "r");
	//重新定位文件指针
	//SEEK_END文件末尾，0偏移量
	fseek(fp,0,SEEK_END);
	//返回当前的文件指针，相对于文件开头的位移量
	long filesize = ftell(fp);
	printf("%d\n",filesize);

	getchar();
}

```

常用的文件操作也就上面的几种了，有关文件的加密和解密，二进制文件的加密和解密，可能后面应该会结合JNI调用和NDK开发和实现的，好了，这篇文章到此为止！


# CSDN
### 更多系列相关文章传送门：


- [C语言（一）基本数据类型](http://blog.csdn.net/u011974987/article/details/52138798) 
- [C语言（二）有符号数和无符号数的理解](http://blog.csdn.net/u011974987/article/details/52142794) 
- [C语言（三）字符串处理函数](http://blog.csdn.net/u011974987/article/details/52200178)
- [C语言（四）指针概念的理解](http://blog.csdn.net/u011974987/article/details/52270018)
- [C语言（五）内存的分配与释放](http://blog.csdn.net/u011974987/article/details/52290724)
- [C语言（六）结构体和联合体](http://blog.csdn.net/u011974987/article/details/52305364)
- [C语言（七）文件的相关操作](http://blog.csdn.net/u011974987/article/details/52354074)

