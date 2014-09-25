---
layout: post
title: Practice on Programming笔记（第三周：类和对象进阶）
category: Cpp
description: 类和对象进阶
tags: ["C++"]
---

### 复制构造函数(copy constructor)
1.基本概念  
(1)只有一个参数，即对同类对象的引用。  
(2)形如X::X( X & )或者X::X(const X &) //后者能以常量对象作为参数  
(3)若没定义，编译器生成默认复制构造函数。  

```
class Complex{  
    private:  
        double real,imag;  
}  
Complex c1;//调用缺省无参构造函数    
Complex c2(c1);//调用缺省的复制构造函数，将c2初始化为c1;把c1的所有成员变量复制给c2 
```
   
(4)如果定义的自己的复制构造函数，则默认的复制构造函数不存在。

```
class Complex{  
    public:   
        double real,imag;  
    Complex(){}  
    Complex(const Complex &c){  
        real = c.real;  
        imag = c.imag;  
        cout<<"Copy Constructor called";  
    }  
};  
Complex c1;  
Complex c2(c1);  
```

*注意，只能是本类的引用&，不允许形如X::X( X )的构造函数。

2.复制构造函数起作用的三种情况  

(1)用一个对象去初始化同类的另一个对象

```
Complex c2(c1);
Complex c2 = c1;//等价，初始化语句，非赋值语句 
c2 = c1;//赋值语句，c2已生成，无需构造函数
```

(2)如果某函数有一个参数是类A的对象，那么该函数被调用时，类A的复制构造函数将被调用。

```
void Func (A a1){}  
int main(){  
    A a2;  
    Func(a2);//若类A的构造函数不是默认的，Func的形参不一定和a2相等    
    return 0;  
}  
```

(3)如果函数的返回值是类A的对象时，返回时A的复制构造函数被调用

```
class A{  
    public:  
      int v;  
      A(int n){v = n;};  
      A(const A & a){  
          v = a.v;  
          cout<<"Cypy constructor called"<<endl;  
      }  
}  

A Func(){  
    A b(4);  
    return b;  
}  
int main(){  
    A a=Func();  
    cout<<a.v<<endl;  
    return 0;  
}
```
  
输出结果：

```
Copy constructor called
4
```

3.为什么要自己写复制构造函数？
面向对象有时候必须要自己写复制构造函数，否则会出错

### 类型转换构造函数
1.
(1)目的:实现类型的自动转换  
(2)特点: -只有一个参数 -不是复制构造函数  
(3)编译系统会自动调用 -> 转换构造函数 -> 建立一个 临时对象/临时变量  

```
class Complex{  
    public:  
    double real,imag;  
    Complex(int i){//类型转换构造函数  
        cout<<"intConstructor called"<<endl;  
        real = i; imag = 0;  
    }  
    Complex(double r,double i){  
        real = r; imag = i;      
    }  
};  
int main(){  
    Complex c1(7,8);  
    Complex c2 = 12;//类型转换，对c2进行初始化，不对应生成临时对象  
    c1 = 9; //赋值语句，类型不同，自动调用类型转换，生成临时对象，以9为实参，调用Complex(int i)初始化，将临时对象的值赋值给c1  
    cout<<c1.real<<","<<c1.imag<<endl;  
    return 0;  
}  
```


### 析构函数(Destructor)
1.成员函数的一种:
(1)名字与类名相同,在前面加"~"
(2)没有参数和返回值
(3)一个类最多只有一个析构函数 

2.概念
(1)对象消亡时 -> 自动被调用 -> 善后工作，释放分配的空间等
(2)定义了析构函数，编译器不生成缺省析构函数
(3)定时类时没写，编译器生成缺省析构函数 - 不会做什么，不会帮助释放用户申请的内存

3.

```
class String{  
    private:  
        char * p; 
    public:  
        String(){  
            p = new char[10];  
        } 
        ~String();  
}  
String::~String(){  
    delete [] p;//数组得写[],否则只delete了指针第一个  
}  
```

4.析构函数和数组  
(1)对象数组生命期结束时 -> 对象数组的每个元素的析构函数都会被调用

```
class Ctest{  
    public:  
     ~Ctest(){cout<<"destructor called"<<endl;}  
}  
int main(){  
    Ctest array[2];  
    cout<<"End Main"<<endl;  
    return 0;  
}
```
  
输出：

```
End Main  
destructor called  
destructor called
```
  
(2) 

```
Ctest * pTest;  
pTest = new Ctest;//构造函数调用    
delete pTest;//析构函数调用   
-----  
pTest = new Ctest[3];//构造函数调用3次    
delete [] pTest;//析构函数调用3次 
```
 

5.例题 

```
class Demo{  
    int id;  
  public:  
    Demo(int i){  
        id = i;  
        cout<<"id="<<id<<"Constructed"<<endl;      
    }  
    ~Demo(){  
        cout<<"id="<<id<<"Destructed"<<endl;  
    }   
};  

Demo d1(1);  
void Func(){  
    static Demo d2(2);//静态在整个程序结束时消亡 
    Demo d3(3);  
    cout<<Func"<<endl;  
};  
   
int main(){  
    Demo d4(4);  
    d4 = 6; //临时对象 id=6constructed和destructed  
    cout<<"main"<<endl;  
    {  Demo d5(5);  } //自己的作用域，对象生命周期离开后消亡  
    Func();  
    cout<<"main ends"<<endl;  
    return 0;  
} 
```
 
输出: 

```
id = 1 Constructed  
id = 4 Constructed 
id = 6 Constructed  
id = 6 Destructed  
main 
id = 5 Constructed
id = 5 Destructed  
id = 2 Constructed  
id = 3 Constructed  
Func  
id = 3 Destructed  
main ends  
id = 6 Destructed  
id = 2 Destructed  
id = 1 Destructed //先被构造的对象最后被析构 
```
 

### 静态成员变量(Static Variable)和静态成员函数(static member function)
1.静态成员：加static关键字 

```
class CRect{  
    private:  
      int w,h;  
      static int nTotalArea;//静态成员变量    
      static int nTotalNumber;  
    public:  
      CRect(int w_,int h_);  
      ~CRect();  
      static void PrintTotal();//静态成员函数    
}  
```

2.普通成员变量每个对象各自有一份，静态成员变量一共就一份，为所有对象共享。

3.sizeof运算符不会计算静态成员变量 

```
class CMyclass{
    int n;
    static int s;
}
```

则sizeof(CMYclass)等于4  

4.普通成员函数必须具体作用于某个对象，而静态成员函数并不具体作用于某个对象 
*因此静态成员不需要通过对象就能访问 

5.如何访问静态成员 
(1)类名::成员名 
CRect::PrintTotal();//访问函数 
CRect::nTotalArea;//访问变量 
(2)对象名.成员名 / 引用.成员名 
CRect r; r.PrintTotal();//并不是作用在r对象上，只是个形式
(3)指针->成员名
CRect * p = &r; p->PrintTotal();  

6.基本概念
(1)本质上是全局变量，哪怕一个对象都不存在，类的静态成员变量也存在
(2)静态成员函数本质上是全局函数
(3)目的将和类紧密相关的全局变量(Global variable)和函数写到类里，易维护和理解(封装)

7.函数例子(续)

```
CRect::CRect(int w_,int h_){  
    w = w_;  
    h = h_;  
    nTotalNumber ++;  
    nTotalArea += w*h;  
}  
CRect::~CRect(){  
    ntotalNumber --;  
    nTotalArea -= w*h;  
}  
void CRect::PrintTotal(){  
    cout<<nTotalNumber<<","<<nTotalArea<<endl;  
}
```  

8.必须在定义类的文件中对静态成员变量进行一次声明或初始化，否则编译能通过，链接不能通过(?)

```
int CRect::nTotalNumber = 0;  
int CRect::nTotalArea = 0;  
int main(){  
    CRect r1(3,3),r2(2,2);  
    //cout<<CRect::nTotalNumber;//Wrong,私有  
    CRect::PrintTotal();  
    r1.PrintTotal();  
    return 0;  
} 
``` 

9.注意:在静态成员函数中，不能访问非静态成员变量（不是作用于某对象上的），也不能调用非静态成员函数。

```
void CRect::PrintTotal(){  
    cout<<w<<","<<nTotalNumber<<","nTotalArea<<endl;//错的！  
}  
CRect::PrintTotal;//w到底是属于哪个对象的？  
```

10.函数例子(续)的缺陷

(1)有时候会调用*复制*构造函数，隐藏的CRect对象被生成
-调用一个以CRect类对象作为参数的函数时
-调用一个以CRect类对象作为返回值的函数时
*生成的时候没有增加，析构的时候(作为函数返回值的时候)减少了，总数减少了

(2)解决办法：为CRect类写一个复制构造函数

```
CRect::CRect(CRect & r){
    w = r.w; h = r.h;  
    nTotalNumber++;  
    nTotalArea += w*h;  
}  
```

### 成员对象和封闭类
1.概念
(1)成员对象：一个类的成员变量是另一个类的对象
(2)封闭类(Enclosing)：包含成员对象的类

2.例子

```
class CTyre{ //轮胎类  
  private:  
    int radius;  
    int width;  
  public: 
    CTyre(int r, int w):radius(r),width(w){} //初始化新方式：初始化列表  
};  
   
class CEngine{ //引擎类  
}   
   
class CCar{ //汽车类 -> "封闭类"，包含了成员对象  
  private:  
    int price; //价格  
    CTyre tyre;  
    CEngine engine;  
  public:  
    CCar(int p,int tr,int tw);  
};  
CCar::CCar(int p,int tr,int w):price(p),tyre(tr,w){};  
int main(){  
    CCar car(20000,17,225);  
    return 0;  
}  
```

3.如果CCar类不定义构造函数，则
CCar car; //error, 编译出错
(1)编译器不知道car.tyre该如何初始化
(2)car.engine的初始化没有问题:用默认构造函数
(3)生成封闭类对象的语句->明确"对象中的成员对象"->如何初始化

4.封闭类构造函数的初始化列表
(1)定义封闭类的构造函数时，添加初始化列表：
类名::构造函数(参数表):成员变量1(参数表),成员变量2(参数表),...
{...}
(2)成员对象初始化列表中的参数
-任意复杂的表达式
-函数/变量/表达式中的函数,变量有定义

5.调用顺序
当封闭类对象生成时，
(1)执行所有成员对象的构造函数
(2)执行封闭类的构造函数
成员对象的构造函数调用顺序,
(1)和成员对象在类中的说明顺序一致
(2)与在成员初始化列表中出现的顺序无关
当封闭类的对象消亡时，
(1)先执行封闭类的析构函数
(2)执行成员对象的析构函数
*析构函数顺序和构造函数的调用顺序相反

### 友元(Friend)
1.友元函数
一个类的友元函数可以访问该类的私有成员

```
class CCar; //提前声明CCar类
class CDriver{  
  public:  
    void ModifyCar(CCar * pCar);//改装汽车  
};  
class CCar{ 
  private:  
    int price;  
  friend int MostExpensiveCar(CCar cars [], int total);//声明友元    
  friend void CDriver::ModifyCar(CCar * pCar); 
}; 
``` 

2.将一个类的成员函数(包括构造，析构函数)定义为另一个类的友元  

```
class B{  
  public:  
    void function();  
};  
class A{  
  friend void B::function();  
}; 
```   

3.友元类
A是B的友元类->A的成员函数可以访问B的私有成员

```
class CCar{  
  private:  
    int price;  
  friend class CDriver; //声明CDriver为友元类  
};  
class CDriver{  
  public:  
    CCar myCar;  
    void ModifyCar(){ //改装汽车  
      myCar.price += 1000; //CDriver是CCar的友元类->可以访问其私有成员  
    }  
};  
int main(){return 0;}  
```

*注意：友元之间的关系不能传递，不能继承  

### this指针
1.其作用就是指向成员函数所作用的对象
2.this指针和静态成员函数
静态成员函数中不能使用this指针，因为静态成员函数并不具体作用于某个对象
因此，静态成员函数的真实的参数的个数，就是程序中写出的参数个数

### 常量对象、常量成员函数和常引用
1.如果不希望某个对象的值被改变，则定义该对象的时候在前面加const

2.在类的成员函数说明后面加const，该成员函数成为常量成员函数
(1)常量成员函数执行期间不应该修改其所作用的对象
(2)因此，常量成员函数中不能修改成员变量的值(静态成员除外，不属于对象一部分)
(3)不能调用同类的非常量成员函数(非常量成员函数可能修改成员变量，静态成员函数除外)

3.例子

```
class Sample{  
  public:  
    int value;  
    void GetValue() const;  
    void func(){};  
    Sample(){};  
};  
void Sample::GetValue() const {  
   value = 0; //wrong  
   func();//wrong  
}  
int main(){  
const Sample o;  
o.value = 100; //error  
o.func(); //error  
o.GetValue();//OK  
return 0;  
}//Dev C++中要为Sample类编写无参构造函数，visual 2010中不要  
```


4.常量成员函数的重载
(1)两个成员函数，名字和参数表都一样，但是一个是const，一个不是，算重载

5.常引用
(1)引用前面加const，不能修改引用的变量
(2)对象作为函数的参数时，生成该参数需要调用复制构造函数，效率低； 用指针做参数代码难看，所以用常引用
