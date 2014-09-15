---
layout: post
title: Practice on Programming笔记（第二周:类和对象初探）
category: 笔记
description: Practice on Programming笔记
tags: ["C++"]
---

### 面向对象程序设计方法

1. 结构化程序设计：大问题分解模块化，成子问题

2. 程序 = 数据结构+算法

3. 结构化程序的问题
(1)理解难
(2)修改难
(3)查错难
(4)重用难

4. 所以需要面向对象
面向对象程序 = 类+类+类

5. 方法
一类事物=>抽象=>共同属性(数据结构)+行为(函数)=>封装=>类

6. 类定义的变量(variable)->类的实例(instance)->“对象(object)”

7. 对象的内存分配
对象的大小 = 所有成员变量的大小之和

8. 对象间的运算
(1)可以用"="进行赋值
(2)不能用"==""!=""<"">"等进行复制，除非运算符重载(operator overloading)

9. 访问类的成员变量和成员函数
(1)对象名.成员名

```
CRect r1,r2;
r1.w = 5;
r2.Init(3,4);
```

(2)指针->成员名

```
CRect r1,r2;
CRect * p1 = & r1;
CRect * p2 = & r2;
p1->w = 5;
p2->Init(3,4);
```

(3)引用名.成员名
和(1)一样

10. 对象可当参数来传递

11. 类的成员函数的另一种写法-成员函数体和类的定义分开写

```
class CRect
{
    public:  
   int w,h;  
   int Area();  
   int Perimeter();  
   void Init(int w_,int h_);  
};  

int CRect::Area(){  
    return w*h;  
}  

int CRect::Perimeter(){  
    return 2*(w+h);  
}  

void CRect::Init(int w_,int h_){  
    w = w_; h = h_;  
}  
```

### 类成员的可访问范围

1. 关键字：
private：成员函数内被访问
public:任何地方被访问
protected：指定保护成员 

2. 没关键字成员缺省为私有成员

3. 
(1)类的成员函数内部，可访问
当前对象全部属性，函数
同类*其他对象*的全部属性，函数
(2)类的成员函数以外的地方，只能访问
该类对象的公有成员

4. 
(1)设置私有成员的目的
强制对成员变量的方位一定要通过成员函数进行
(2)设置私有成员的机制--隐藏

### 内联成员函数和重载成员函数
1. 内联成员函数
(1)inline+成员函数
(2)整个函数体出现在类定义内部

```
 class B{  
     inline void func1();  
     void func2()  
    {  
    };  
}     
 void B::func1(){}  
 ```
 

2. 成员函数的重载及参数缺省
(1)重载成员函数
(2)成员函数 -- 带缺省参数

```
#include <iostream>  
 using namespace std;  
 class Location{  
     private:  
           int x,y;  
     public:  
     void init(int x=0,int y=0);  
     void valueX(int val){x=val;}  
     int  valueX(){return x;}  
}  
 void Location::init(int X,int Y){  
     x = X;  
     y = Y;  
}  
```

(3)使用缺省参数要避免函数重载时的二义性

```
 class Location{  
     private:  
           int x,y;  
     public:  
           void init(int x=0,int y=0);  
           void valueX(int val=0){x = val;}  
           int valueX(){return x;}  
};  
 A.valueX();//错误，编译器无法判断调用哪个valueX 
``` 

### 构造函数constructor

1. 名字与类名相同，可以有参数，不能有返回值(void 也不行)

2. 作用：初始化对象，e.g.给成员变量赋初值

3. 定义时没写，编译器生成默认构造函数，默认构造函数无参数，不作任何操作

4. 基本概念
(1)存储空间不是由构造函数分配，构造函数只是在已有存储空间的基础上做初始化工作，“对已有房子的装修”
(2)对象生成时构造函数(默认或自定义)自动被调用。生成后，不能再执行构造函数
(3)一个类可以有多个构造函数

5. 为什么需要构造函数：
执行必要的初始化工作，有了构造函数，就不必专门写初始化函数，不用担心忘记调用初始化函数。（有时没初始化会出错）

6. 例子

```
 class Complex{  
     private:  
         double real,imag;  
     public:  
         Complex(double r,double i=0);  
};  
 Complex::Complex(double r,double i){  
     real = r;imag = i;  
}  
 Complex c1;//error,缺少构造函数参数   
 Complex * pc = new Complex;//error   
 Complex C1(2);//correct   
 Complex c1(2,4);//correct   
 Complex * pc = new Complex(3,4);//correct  
``` 

7. 多个构造函数

```
 class Complex{  
     private:  
         double real,imag;  
     public:  
         void Set(double r,doublr i);  
         Complex(double r,double i);  
         Complex(double r);  
         Complex(Complex c1,Complex C2);  
};  
 Complex::Complex(double r,double i){  
     real = r;imag = i;  
}  
 Complex::Complex(doublr r){  
     real = r; imag = 0;  
}
 Complex::Complex(Complex c1,Complex c2){  
     real = c1.real + c2.real;  
     imag = c1.imag + c2.imag;  
}  
 Complex c1(3),c2(1,0),c3(c1,c2);  
```
 
8. 构造函数在数组中的使用

```
 class Test{  
     public:  
         Test(int n){}      //(1)  
         Test(int n,int m){}//(2)  
         Test(){}           //(3)  
} 
 
 Test array1[3] = {1,Test(1,2)};    
//三个元素分别用(1)(2)(3)初始化   
 Test array2[3] = {Test(2,3),Test(1,2),1};  
//三个元素分别用(2)(2)(1)初始化  
 Test * pArray[3] = {new Test(4),new Test(1,2)};  
//三个元素分别用(1)(2)初始化   
```
