---
layout: post
title: Practice on Programming笔记（第六周：多态与虚函数）
category: 笔记
description: 多态与虚函数
tags: ["C++"]
---

### 多态(Polymorphism)和虚函数(Virtual function)的基本概念

1. 虚函数
(1)virtual关键字
(2)构造函数和静态成员函数不能是虚函数
(3)特点：可以参与多态

2. 多态的表现形式一
(1)派生类(derived class)的指针可以赋给基类指针
(2)通过基类指针调用基类和派生类中的同名虚函数时
-若该指针指向一个基类的对象，那么被调用的是基类的虚函数；
-若该指针指向一个派生类的对象，那么被调用的是派生类的虚函数
-这种机制：多态
(3)例子

```
class CBase{
    public:
	virtual void SomeVirtualFunction(){}
};
class CDerived:public CBase{
	public:
	virtual void SomeVirtualFunction(){}
};
int main(){
	CDerived ODerived;
	CBase * p = & ODerived;
	p->SomeVirtualFunction();//调用哪个虚函数取决于p指向哪种类型的对象
	//这里会执行derived类里的function
	return 0;
}
```

3. 多态的表现形式二
(1)派生类的对象可以赋给基类引用
(2)通过基类引用调用基类和派生类中的同名虚函数时：
-若该引用引用的是一个基类的对象，那么被调用的是基类的虚函数
-若该引用引用的是一个派生类的对象，那么被调用的是派生类的虚函数
-这种机制：多态

4.例子
 
```
class CBase{
    public:
	virtual void SomeVirtualFunction(){}
};
class CDerived:public CBase{
	public:
	virtual void SomeVirtualFunction(){}
};
int main(){
	CDerived ODerived;
	CBase &r = ODerived;
	r.someVirtualFunction();//取决于r引用哪种类型的对象
	return 0;
}
```

5. 多态的作用
在OO中使用多态，能够增加程序的可扩充性，即程序需要修改或者增加功能的时候，需要改动和增加的代码较少。

### 例子

1. 几何形体处理程序
(1)输入若干几何形体的参数，要求按面积从小到大排序输出。

```
#include<iostream>
#include<stdlib.h>
#include<math.h>
using namespace std;

class CShape{
  public:
	virtual double Area()=0;      //纯虚函数，没有函数体
	virtual void PrintInfo()=0;	  //因为不存在一个CShape，不需要成员函数
};

class CRectangle:public CShape{
  public:
	int w,h;
	virtual double Area();
	virtual void PrintInfo();
};

class CCircle:public CShape{
  public:
	int r;
	virtual double Area();
	virtual void PrintInfo();
};

class CTriangle:public CShape{
  public:
	int a,b,c;
	virtual double Area();
	virtual void PrintInfo();
};

double CRectangle::Area(){
	return w*h;
}
void CRectangle::PrintInfo(){
	cout<<"Rectangle:"<<Area()<<endl;
}

double CCircle::Area(){
	return 3.14*r*r;
}
void CCircle::PrintInfo(){
	cout<<"Circle:"<<Area()<<endl;
}

double CTriangle::Area(){
	double p = (a+b+c)/2.0;
	return sqrt(p*(p-a)*(p-b)*(p-c));
}
void CTriangle::PrintInfo(){
	cout<<"Triangle:"<<Area()<<endl;
}

CShape * pShapes[100];
int MyCompare(const void * s1, const void * s2); //函数指针？

int main(){
	int i; int n;
	CRectangle * pr; CCircle * pc; CTriangle * pt;
	cin>>n;
	for(i=0;i<n;i++){
		char c;
		switch(c){
			case'R':
				pr = new CRectangle();
				cin>>pr->w>>pr->h;
				pShape[i]=pr;
				break;
			case'C':
				pc = new CCircle();
				cin>>pc->r;
				pShapes[i] = pc;
				break;
			case'T':
				pt = new CTriangle();
				cin>>pt->a>>pt->b>>pt->c;
				pShapes[i] = pt;
				break;
		}
	}	
	qsort(pShapes,n,sizeof(CShape*),MyCompare);
	for(i=0;i<n;i++){
		pShapes[i]->PrintInfo();
	}
	return 0;
}

int MyCompare(const void * s1,const void * s2){
	double a1,a2;
	CShape ** p1; //s1,s2类型是void*,不可写“*s1”来取得s1指向的内容
	CShape ** p2; 
	//个人感觉：*s指向的是数组中元素的位置，下面的CShape ** s1才是那个位置上的shape

	p1 = (CShape**)s1;//s1,s2指向pShapes数组中的元素，数组元素的类型是CShape*
	p2 = (CShape**)s2;//故p1,p2都是指向指针的指针，类型为CShape **
	a1 = (*p1)->Area();//*p1的类型是CShape *,是基类指针，故此句为多态
	a2 = (*p2)->Area();
	
	if(a1<a2) return -1;
	else if(a1<a2) return 1;
	else return 0;
}

```

-若要添加新的shape，改动很少
-用基类指针数组存放指向各种派生类对象的指针，然后遍历该数组，就能对各个派生类对象做各种操作，是很常用的做法。

(2)例子2

```
class Base{
  public:
	void fun1(){fun2();}
	virtual void fun2(){cout<<"Base::fun2()"<<endl;}
};
class Derived:public Base{
  public:
	virtual void fun2(){cout<<"Derived:fun2()"<<endl;}
};
int main(){
	Derived d;
	Base * pBase = &d;
	pBase->fun1();
	return 0;
}
```

```
输出结果：Derived:fun2()
```

```
class Base{
  public:
	//void fun1(){fun2();} 等价于
	void fun1(){this->fun2();}//this是基类指针，fun2是虚函数，所以是多态
	virtual void fun2(){cout<<"Base::fun2()"<<endl;}
};
class Derived:public Base{
  public:
	virtual void fun2(){cout<<"Derived:fun2()"<<endl;}
};
int main(){
	Derived d;
	Base * pBase = &d;
	pBase->fun1();
	return 0;
}
```

(3)得出结论：在非构造函数，非析构函数的成员函数中调用虚函数，是多态！

(4)构造函数和析构函数中调用虚函数
不是多态。编译时即可确定，调用的函数是自己的类或基类中定义的函数，不会等到运行时才决定调用自己的还是派生类的函数。

(5)再一个例子
(派生类中和基类中虚函数同名同参数表的函数，不加virtual也自动成为虚函数)

```
class myclass{
  public:
	virtual void hello(){cout<<"hello from myclass"<<endl;};
	virtual void bye(){cout<<"bye from myclass"<<endl;}
};
class son:public myclass{
  public:
	void hello(){cout<<"hello from son"<<endl;};
	son(){hello()};
	~son(){bye()};
};
class grandson:public son{
  public:
    void hello(){cout<<"hello from grandson"<<endl;};
	void bye(){cout<<"bye from grandson"<<endl;};
	grandson(){cout<<"constructing grandson"<<endl;};
	~grandson(){cout<<"destructing grandson"<<endl;};
};

int main(){
grandson gson;
son *pson;
pson = &gson;
pson->hello();//多态
return 0;
}

```

```
输出结果：
hello from son //从顶往下构造，myclass到son到grandson，son的构造函数输出hello from son
constructing grandson
hello from grandson
destructing grandson
bye from my class
```

在构造函数和析构函数中调用虚函数，不是多态。
编译时即可确定，调用的函数是自己的类或基类中定义的函数，
不会等到运行时才决定调用自己的还是派生类的函数。

*总之，在*运行时*决定调用的虚函数才是多态。

### 多态实现原理
1. 动态联编
多态的关键在于通过基类指针或引用调用一个虚函数时，编译时不确定要调用的是基类还是派生类的函数，运行时才能确定，这叫做动态联编
dynamic dispatch(?) is the process of selecting which implementation of a polymorphic operation (method or function) to call at runtime

2. 网上资料
   将一个调用函数连接上正确的被调用函数，这个过程就叫做函数的联编，简称联编。在C++中，一共有两种联编的方式：

静态联编
define：静态联编是指联编工作出现在编译连接阶段。
特点：① 静态联编就是指被调用函数和执行调用函数之间的关系以及它们在内存中的地址在编译的时候已经确定好了，运行时不会发生变化。
      ② 由于对象不用对自身进行跟踪，因此速度浪费比较小，但是灵活性较差。
动态联编
define：动态联编是指在程序运行的时候才进行的联编工作。
特点：① 由于编译程序在编译阶段并不能确切知道将要调用的函数，只有在程序执行时才能确定将要调用的函数。要确切之道该调用的函数，就必须要求联编工作在程序运行时才能进行。
      ② 虽然可以追踪对象，灵活性较强，但是速度浪费严重。

3. 动态联编的实现（例子）

```
class Base{
  public:
    int i;
	virtual void Print(){cout<<"Base:Print";}
};
class Derived: public Base{
  public:
    int n;
	virtual void Print(){cout<<"Derived:Print"<<endl;}
};

int main(){
	Derived d;
	cout<<sizeof(Base)<<","<<sizeof(Derived);
	return 0;
}
```

预想结果：4,8
输出结果: 8,12

3. 多态实现的关键 -- 虚函数表（virtual function table）
每一个有虚函数的类(或有虚函数的类的派生类)，都有一个虚函数表；
该类的任何对象中都放着虚函数表的指针，虚函数表中列出了该类的虚函数地址；
多出来的4个字节就是用来放虚函数表的地址的（就可以查到并且调用虚函数了）

4. 由于如此，多态有额外的时间空间开销；
空间开销：每一个有虚函数类的对象里，都多出4个字节，存放虚函数表地址；
时间开销；查虚函数表的过程

### 虚析构函数
1. 问题：base class指针指向derived class, delete derived class object时, 
derived class的destructor不会被调用; 只调用基类的destructor
（计数时可能有误差，或者动态内存分配后没有被回收）

```
class CSon{
	public:~CSon(){};
};
class CGrandson:CSon{
	public:~CGrandson(){};
};

int main(){
	CSon *p = new CGrandson;
	delete p;
	retrun 0;
}
```

2. 虚析构函数
(1)删除一个派生类的对象时，先调用派生类的析构函数，再调用基类的析构函数

(2)解决办法：
把基类的析构函数声明为virtual（派生类的析构函数virtual可以不进行声明）
通过基类的指针删除派生类对象时，首先调用派生类的析构函数，然后调用基类的析构函数

(3)类如果定义了虚函数，则最好将析构函数也定义成虚函数
*不允许以虚函数作为构造函数

3. 再一个例子

```
class son{
  public:
    ~son(){cout<<"bye from son"<<endl;};
};
class grandson : public son{
  public:
    ~grandson(){cout<<"bye from grandson"<<endl;};
};

int main(){
	son *pson;
	pson = new grandson;
	delete pson;
	retrun 0;
}
```

输出结果：
```
bye from son
```
没有执行grandson::~grandson()

4. 解决

```
virtual ~son(){cout<<"bye from son"<<endl;};
```

执行grandson::~grandson(),引起执行son::~son()
输出结果：

```
bye from grandson 
bye fron son
```

### 纯虚函数和抽象类
1. 纯虚函数：没有函数体的虚函数

```
class A{
  private:
    int a;
  public:
    virtual void Print() = 0; //纯虚函数,没有{...}
	void fun(){cout<<"fun";}
}
```

2. 抽象类：包含纯虚函数的类
(1)只能作为基类来派生新类使用
(2)不能创建抽象类的对象
(3)抽象类的指针和引用->有抽象类派生出来的类的对象

```
A a; //错，A是抽象类，不能创建对象
A *pa; //ok，可以定义抽象类的指针和引用
pa = new A; //错误，A是抽象类，不能创建对象
```

3. 纯虚函数和抽象类
(1)抽象类中，
-成员函数可以调用纯虚函数
-在构造函数/析构函数内部不能调用纯虚函数

(2)如果一个类从抽象类派生而来
它实现了基类中的所有纯虚函数，才能成为非抽象类 （即使填个{}也算实现了纯虚函数）

```
class A{
  public:
    virtual void f()=0;//纯虚函数
	void g(){this->f();}//ok
	A(){}//构造函数不能调用f();错误
}
class B:public A{
  public:
    void f(){cout<<"B:f()"<<endl;}
};

int main(){
	B b; //B不是抽象类，可以实现
	b.g();
	return 0;
}
```

输出结果：
```
B:f()
```

