---
layout: post
title: Practice on Programming笔记（第七周：模板,string类）
category: C++
description: 函数模板和类模板
tags: ["C++"]
---


### 泛型程序设计(generic programming)
1. 算法实现时不指定具体要操作的数据的类型

2. 泛型：算法实现一遍；适用于多种数据结构
*优势：减少重复代码的编写

3. （资深程序员）大量编写模板，使用模板的程序设计
(1)函数模板
(2)类模板

### 函数模板(function template)
1. 

```
template<class 类型参数1，class 类型参数2，...> //写出会出现的类型参数？
返回值类型 模板名(形参表)
{}
```

2. swap函数写个模板

```
template <class T>
void Swap(T & x, T & y)
{
	T tmp = x;
	x=y;
	y=tmp;
}

int main(){
	int n=1,m=2;
	Swap(n,m);//编译器自动生成void Swap(int &,int &)函数
	double f=1.2,g=2.3;
	Swap(f,g);//编译器自动生成void Swap(double &,double &)函数
	return 0;
}

```

3. 不止一个类型参数

```
template<class T1,class T2>
T2 print(T1 arg1,T2 arg2)
{
	cout<<arg1<<" "<<arg2<<endl;
	return arg2;
}
```

4. 求数组最大元素

```
template<class T>
T MaxElement(T a[],int size)
{
	T tmpMax=a[0];
	for(int i=1;i<size;++i)
	{
		if(tmpMax<a[i]) tmpMax=a[i];
	}
	return tmpMax;
}
```

5. 函数模板重载(只要形参表不同即可)

```
template<class T1,class T2>
void print(T1 arg1,T2 arg2)
{}

template<class T>
void print(T arg1,T arg2)
{}
```

6. C++编译器遵循的优先顺序
(1)先找参数完全匹配的普通函数
(2)再找参数完全匹配的模板函数
(3)再找实参经过自动类型转换后能够匹配的普通函数
(4)都找不到，报错

7. 避免类型参数的二义性

```
template<class T>
T myFunction(T arg1,T arg2)
{
	return arg1;
}
```

```
myFunction(5,7);//T is int
myFunction(5.8,5.7);//T is double
myFunction(5,8.4)//error T is double?int?
```

二义性出现，需要修改

```
template<class T1,class T2>
T1 myFunction(T1 arg1,T2 arg2)
{}
```

### 类模板
1. 类模板

```
template<类型参数表>
class 类模板名
{
	成员函数和成员变量
};
```

类型参数表的写法：

```
class 类型参数1，class 类型参数2
```

2. 类模板的成员函数，如在类模板外定义时

```
temlate<类型参数表>
返回值类型 类模板名<类型参数名列表>::成员函数名(参数表)
{...}
```

3. 类模板的定义
(1)用类模板定义对象的写法：
类模板名<真实类型参数表> 对象名(构造函数实际参数表);
(2)无参构造函数
类模板名<真实类型参数表> 对象名;

4. 例子：Pair类模板

```
template<class T1,class T2>
class Pair{
  public:
	T1 key;
	T2 value;
	Pair(T1 k,T2 v):key(k),value(v){}; //constructor
	bool operator<(const Pair<T1,T2> & p)const;
};
template<class T1,class T2>
bool Pair<T1,T2>::operator<(const Pair<T1,T2> & p)const //不改变对象的值所以用const
{
	return key<p.key;
}
```

类模板的使用：

```
int main(){
	Pair<string,int> student("Tom",19);
	//实例化出一个类Pair<string,int>的模板类
	cout<<student.key<<" "<<student.value;
	return 0;
}
```

5. 概念：使用类模板声明对象
(1)编译器由类模板生成类的过程：类模板的实例化
-编译器自动用具体的数据类型，替换类模板中的类型参数，生成模板类的代码
(2)由类模板实例化得到的类叫：模板类
-为类型参数指定的数据类型不同，得到的模板类不同。

6. 同一个类模板的两个模板类是不兼容的
Pair<string,int> *p;
Pair<string,double> a;
p = &a;//wrong

7. 函数模板作为类模板成员

```
#include <iostream>
using namespace std;
template<class T>
class A{
  public:
	template<class T2>
	void Func(T2 t){cout<<t;}//成员函数模板
}；

int main(){
	A<int> a;
	a.Func('K');成员函数模板Func被实例化
	return 0;
}
```
*类型参数不能一致，必须写成T，T2

8. 类模板的参数声明中可以包括非类型参数
template<class T,int elementsNumber>
-非类型参数：用来说明类模板中的属性
-类型参数：用来说明类模板中的属性类型，成员操作的参数类型和返回值类型

9. 类模板的“<类型参数表>”中可以出现非类型参数：

```
template <class T,int size>
class CArray{
	T array[size];
  public:
	void Print()
	{
		for(int i=0;i<size;i++)
			cout<<array[i]<<endl;
	}
}
```

注意：
CArray<double,40> a2;
CArray<int,50> a3;
a2和a3是不同的模板类
CArray<int,40>和CArray<int,50>也是不同的模板类

10. 类模板与继承
(1)类模板-派生-类模板
(2)模板类-派生-类模板
(3)普通类-派生-类模板
(4)模板类-派生-普通类

### string