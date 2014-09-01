---
layout: post
title: Practice on Programming笔记（第一周:从C到C++）
category: 笔记
description: Practice on Programming笔记
tags: ["C++"]
---

### 位运算

1. 单/双目运算符：
运算所需变量为两个的运算符叫做双目运算符

2. 这种类型的数字：
0xffffff00表示低八位全是0，剩下的是1(16进制表示的二进制)

3. 按位异或
(1)简单的加密解密：a^b=c,可以推出c^a=b,c^b=a
(2)不用临时变量，交换两个变量的值：

```
int a=5,b=7;
a=a^b;
b=b^a;
a=a^b;
```

或者按我的理解

```
a=a^b;
b=a^b;
a=a^b;
```


4. 左移运算符“<<”
e.g. 9<<4 9的二进制左移4位，高位抛弃，低位补零
左移一位，数字乘以2；左移n位，乘以2^n;比乘法操作快得多

5. 右移运算符“>>”
对于有符号数，高位为1，右移补1；高位为0，右移补0；无符号数，补0
右移n位，数字除以2^n,结果按floor取整（往小取整）

6. 二进制负数
补码：反码加1称为补码
负数以其正值的补码形式表达

7. 练习
(1)获得数字a的二进制的第n位数值(0<=n<=31)

```
(a>>n)&1
```

(2)获得数字a的二进制的第n位数值(0<=n<31)

```
(a&(1<<n))>>n
```

8. 符号位
一个字节只有7位（除去符号位） 
分配一个符号位来表示这个符号：设置这个位（通常为最高有效位）为0表示一个正数，为1表示一个负数

### 引用

1. int n = 4; int & r = n; r的类型是int &
某个变量的引用，等价于这个变量
(1)必须初始化成引用某个变量
(2)初始化后一直引用，不会再变
(3)只能引用变量

2. 两个例子
(1)

```
 void swap(int *a,int *b)
 { 
	int tmp; 
	tmp = *a;*a = *b;*b = tmp; 
 } 
 int n1,n2; 
 swap(&n1,&n2); 
 ```
 
(2)

```
 void swap(int &a,int &b) 
 { 
	int tmp; 
	tmp = a; a = b; b = tmp; 
 } 
int n1,n2; 
swap(n1,n2); 
```


3. 引用作为函数的返回值
e.g.

```
 int n = 4; 
 int & SetValue(){return n;} //引用了返回值n 
 int main(){ 
	SetValue()=40; 
	cout<<n; 
	return 0; 
 } 
```

4. 常引用（加const）

```
 int n; 
 const int &r = n; 
 r = 200;//编译出错 
 n = 300;//没问题 
```
 
r的类型：const int &
特点：不能通过常引用去修改其引用的内容

5. 常引用和非常引用的转换
(1)const T & 和 T & 是不同的类型
(2)T&类型的引用 或 T类型的变量，可用于const T &的初始化
(3)const T 类型的常变量和const T & 类型的引用不能用来初始化T&类型的引用，除非强制转换 

### const关键字和常量

1. 定义常量 

```
const int MAX_VAL = 23;
const char * NAME = "school";
const string NAME = "school";
```

2. 定义常量指针
(1)不可通过常量指针修改其指向的内容

```
 int n,m; 
 const int *p = &n; 
 *p = 5;//编译出错 
n = 4;//没问题 
p = &m;//指向别的内容，没问题 
```

(2)不能把常量指针赋值给非常量指针，反过来可以

```
 const int *p1; 
 int * p2; 
 p1 = p2;//ok 
 p2 = p1;//error 
 p2 = (int*) p1; //ok,强制类型转换 
```

(3)函数参数为常量指针时，可避免函数内部对参数指针所指内容的修改

### 动态内存分配

1. C语言：malloc；C++：P = new T;
分配出大小为sizeof(T)字节(byte)的内存空间，并将起始地址赋值给P

```
 int * pn; 
 pn = new int; 
 * pn = 5; 
```

2. 分配数组

```
P = new T[N];
```

动态分配出大小为N*sizeof(T)字节(byte)的内存空间，并将起始地址赋值给P

3. new 运算符返回值的类型是指针 T *

4. delete释放空间
delete 指针;//必须指向new出来的空间；且不能delete两次
delete [] 指针;//指向数组
delete 指针；//指向数组，并没有完全delete，产生内存垃圾碎片

###内联函数和重载函数
1. 内联函数(Inline function)
(1)
函数调用有时间开销：调用函数时，参数入栈(stack)，返回地址入栈，执行返回后，从栈取出返回地址，跳转返回地址去执行。
反复执行函数，开销增大。
所以引入内联函数：编译器处理时，将整个函数代码插入调用语句处，不产生调用函数的语句。
(2)

```
 inline int Max(int a,int b) 
 { 
	if(a>b) return a; 
	return b; 
 } 
```

(3)坏处：可执行程序体积增大
e.g.

```
 k = Max(n1,n2); 
 if (n1>n2) tmp = n1; 
 else tmp = n2; 
 k = tmp; 
```


2. 函数重载(Function overloading)
(1)函数类型相同，参数个数或参数类型不同
(2)函数命名简单，编译器根据实参个数和类型判断调用哪个函数/
(3)必须是函数名相同，参数不同；若参数相同，返回值类型不同，不是重载，是重复定义

3. 函数的缺省参数
(1)定义函数的时候让最右边的连续若干个参数有缺省值。
void func(int x1, int x2 = 2,int x3 = 3) {}
(2)提高可扩充性


