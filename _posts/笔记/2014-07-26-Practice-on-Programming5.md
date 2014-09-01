---
layout: post
title: Practice on Programming笔记（第五周：继承与派生）
category: 笔记
description: 继承与派生
tags: ["C++"]
---

###继承与派生  

1.继承(inheritance)
(1)B类拥有A类的全部特点
(2)把A作为一个基类(base),B作为基类的一个派生类(子类)
  
2.派生(derived)
(1)派生类是通过对基类进行修改和扩充(新的成员变量和成员函数)得到的.
(2)派生类一经定义后,可以独立使用,不依赖于基类.
(3)拥有基类的全部成员:private,proteted,public
(4)但是不能访问基类中的private成员
(5)可以访问protected?

```
/*派生类的写法*/

class 派生类名:public 基类名
{
};
```

```
/*例子*/

class CStudent{
private:
	string sName;
	int nAge;
public:
	bool IsThreeGood(){};
	void SetName(const string & name) {sName=name;}
	//...
};

class CUndergraduateStudent: public SCtudent{
private:
	int nDepartment;
public:
	bool IsThreeGood(){...}; //覆盖 override? 
	bool CanBaoYan(){...};
};//派生类的写法是: 类名:public 基类名

class CGraduatedStudent:public CStudent{
private:
	int nDepartment;
	char szMentorName[20];
public:
	int CountSalary(){...};
};
```

3.派生类对象的内存空间
(1)派生类对象的体积=基类对象的体积+派生对象自己成员变量的体积
(2)派生类对象中,包含着基类对象; 基类对象的储存位置位于派生类对象新增的成员变量之前

```
/*例子*/

class CBase{
	int v1,v2;	//8 byte
};

class CDerived:public CBase
{
	int v3;		//4 byte
};
```

4.继承实例程序:学籍管理

```
#include <iostream>
#include <string>
using namespace std;

class CStudent{
private:
	string name;
	string id;
	char gender;
	int age;
public:
	void PrintInfo(){
		//print name,id,gender,age
		cout<<name<<id<<gender<<age<<endl;
	};
	void SetInfo(const string & name_,const string & id_,int age_,char gender_)
	{...};
	string GetName(){return name;}
};

class CUndergraduateStudent:public CStudent{
private:
	string department;
public:
	void QualifiedForBaoyan(){cout<<"qualified for baoyan"<<endl;}
	void PrintInfo(){//是override,derived class对base class的修改;不是overload,overload要求params table一样
		CStudent::PrintInfo();
		cout<<"Department:"<<department<<endl;
	}
	void SetInfo(const string &name_,const string & id_,int age_,char gender_,const string & department_){
		CStudent::SetInfo(name_,id_,age_,gender_);//调用基类同名函数
		department = department_;
	}
}

int main(){
	CUndergraduateStudent s2;
	s2.SetInfo("Harry","1111",19,'M',"Computer Science");
	cout<<s2.GetName()<<"";
	s2.QualifiedForBaoyan();
	s2.PrintInfo();
}
```

### 复合关系和继承关系
1. 类之间的两种关系

(1)继承: "is"的关系
-base class:A; derived class:B;
-B is A:逻辑上要求,一个B对象也是一个A对象(前面的例子, 本科生是学生)

(2)复合: "has"的关系
-类C中的成员变量k是类D的对象
-D对象是C对象的固有属性或组成部分

```
/*错误使用继承关系的例子*/

class CPoint{
	double x,y;
};

class CCircle:public CPoint{
	double r;
};

//分析:圆并不是一个点
```

```
/*要使用复合关系*/

class CPoint{
	double x,y;
};

class CCircle{
	double r;
	CPoint center;
};
```

```
/*进一步修改*/

class CPoint{
	double x,y;
	friend class CCircle;
};

class CCircle{
	double r;
	CPoint center;
};
```

2. 复合关系的使用例子

```
/*  "狗"和"业主"  */

class CMaster;	//提前声明

class CDog{
	CMaster * pm;
};

class CMaster{
	CDog * dogs[10];
};

//一个类有另一个类的指针, "知道"关系
```

### 基类/派生类同名成员与Protected关键字

1.例子

```
class CBase{
	int j;
public:
	int i;
	void func();
};

class CDerived:public base{
public:
	int i;
	void access();
	void func();
};

void CDerived::access()
{
	j = 5;			//报错,因为j是CBase私有的
	i = 5;			//引用的是派生类的i
	CBase::i = 5;	//引用的是基类的i
	func();			//派生类的
	CBase::func();	//基类的
}

int main(){
	CDerived obj;
	obj.i = 1; 		//派生类的
	obj.base::i = 1;//基类的
}

//obj占用的存储空间: 3*4byte
//Note:一般来说,基类和派生类不定义同名成员变量
```

2. 访问范围说明符
可被函数访问总结:

(1)基类的private成员:
-基类的成员函数
-基类的友元函数

(2)基类的public成员:
-基类成员函数
-基类友元函数
-派生类成员函数
-派生类友元函数
-其他函数

(3)基类的protected成员
-基类的成员函数
-基类的友元函数
-派生类的成员函数 (可以访问*当前对象*的基类的保护成员)

```
/*protected例子*/

class Father{
private:
	int nPrivate;
public: 
	int nPublic;
protected:
int nProtected;
};

class Son:public Father{
	void AccessFather(){
		nPublic = 1;	//ok
		nPrivate = 1;	//wrong
		nProtected = 1;	//OK
		Son f;
		f.nProtected = 1;//WRONG: f不是*当前*对象
	}
};

int main(){
	Father f;
	Son s;
	
	f.nPublic = 1;		//ok
	s.nPublic = 1;		//ok
	f.nProtected = 1;	//ERROR
	f.nPrivate = 1;		//ERROR
	s.nProtected = 1;	//ERROR
	s.nPrivate = 1;		//ERROR

	return 0;
}
```

### 派生类的构造函数
1. 派生类对象包含基类对象
2. 执行派生类构造函数之前, 先执行基类的构造函数

```
/*派生类的构造函数*/

class Bug{
private:
	int nLegs;
	int nColor;
public:
	int nType;
	Bug(int legs,int color);
	void PrintBug(){}:
}

class FlyBug:public Bug{
	int nWings;
public:
	FlyBug(int legs,int color,int wings);
}

/*构造函数们*/
Bug::Bug(int legs,int color){
	nLegs = legs;
	nColor=color;
}

//错误的FlyBug构造函数
FlyBug::FlyBug(int legs,int color,int wings){
	nLegs = legs;		//不能访问
	nColor = color;		//不能访问
	nType = 1;			//OK
	nWings = wings;
}

//正确的FlyBug构造函数
FlyBug::FlyBug(int legs,int color,int wings):Bug(legs,color){
	nWings = wings;
}
```

3.调用基类构造函数的两种方式
(1)显式
-派生类的构造函数给基类的构造函数提供参数
derived::derived(arg_derived-list):base(arg_base-list)

(2)隐式
-派生类的构造函数中圣罗基类构造函数
-自动调用基类的默认构造函数

4.派生类的析构函数被执行时, 析构完完派生类,自动调用基类的析构函数

5.包含成员对象的派生类的构造函数

```
class Skill{
public:
	Skill(int n){}
};

class FlyBug:public Bug{
	int nWings;
	Skill sk1,sk2;
public:
	FlyBug(int legs,int color,int wings);
};

FlyBug::FlyBug(int legs,int color,int wings): Bug(legs,color),sk1(5),sk2(color){
	nWings=wings;
};
```

6.
(1)创建派生类对象时, 执行派生类的构造函数之前:
-调用基类的构造函数(base class)
--初始化派生类对象中从基类继承的成员
-调用成员对象类的构造函数(member objects)
--初始化派生类对象中成员对象

(2)执行完派生类的析构函数后:
-调用成员对象类的析构函数(member objects)
-调用基类的析构函数(base class)

*析构函数的调用顺序与构造函数的调用顺序相反

### public继承的赋值兼容规则
1. 公有派生

```
class base{};
class derived:public base{};//公有派生
base B;
derived D;
```

规则
(1)派生类对象可以复制给基类对象: 
B = D;  
//因为派生类对象是一个基类对象
//但是D = B 是不行的,因为基类对象不是一个派生类对象

(2)派生类对象可以初始化基类引用
base & BR = D;

(3)派生类对象的地址可以赋值给基类指针
base * PB = & D;

*如果派生方式是private或protected,则上述情况都不可行

2. 直接基类与间接基类
(1)声明派生类时,只需列出直接基类
(2)派生类沿着类的层次自动向上继承间接基类
(3)派生类的成员包括
-派生类自己定义的成员
-直接基类中所有成员
-所有间接基类的所有成员