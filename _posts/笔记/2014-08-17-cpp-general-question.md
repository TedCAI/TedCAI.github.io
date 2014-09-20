---
layout: post
title: C++一般向问题汇总
category: 笔记
description: C++一般向问题汇总
tags: ["C++"]
---

### 1. 函数参数
boolean function(input,status,message,log,output)  

### 2. 静态类型转换
int y = static_cast<int>(x);

### 3. 数组元素初始化
memset(grid,false,grid_size*sizeof(bool));

### 4. 多态
polymorphism：the provision of a single interface to entities of different types.A polymorphic type is a type whose operations can also be applied to values of some other type, or types.  

### 5. C++特性
 C++ 除了能Object-oriented programming还能Procedural programming: http://en.wikipedia.org/wiki/Procedural_programming

### 6. 面向对象
OO programming特点（相对于Structured programming）：
 面向对象主要有四大特性：封装、抽象、继承和多态。各自定义： 
封装：在面向对象语言中，封装特性是由类来体现的，我们将现实生活中的一类实体定义成类，其中包括属性和行为（在Java中就是方法），就好像人类，可以具有name,sex,age等属性，同时也具有eat(),sleep()等行为，我们在行为中实现一定的功能，也可操作属性，这是面向对象的封装特性； 
抽象：抽象就是将一类实体的共同特性抽象出来，封装在一个抽象类中，所以抽象在面向对象语言是由抽象类来体现的。比如鸟就是一个抽象实体，因为抽象实体并不是一个真正的对象，它的属性还不能完全描述一个对象，所以在语言中体现为抽象类不能实例化； 
继承：继承就像是我们现实生活中的父子关系，儿子可以遗传父亲的一些特性，在面向对象语言中，就是一个类可以继承另一个类的一些特性，从而可以代码重用，其实继承体现的是is-a关系，父类同子类在本质上还是一类实体； 
多态：多态就是通过传递给父类对象引用不同的子类对象从而表现出不同的行为，多态可为程序提供更好的可扩展性，同样也可以代码重用。

### 7. 实参和形参  
Actual parameters and formal parameters
Actual parameters are parameters as they appear in function calls.
Formal parameters are parameters as they appear in function declarations.

### 8. 字符和字符串
char  
char myword[] = { 'H', 'e', 'l', 'l', 'o', '\0' };  
char myword[] = "Hello";   
const char * NAME = "school";  
const string NAME = "school";

### 9. std命名空间
using namespace std  
使用std命名空间

98年以后的c++语言提供一个全局的命名空间namespace，可以避免导致全局命名冲突问题。举一个实例，请注意以下两个头文件：

// one.h
char func(char);
class String { ... };

// somelib.h
class String { ... };

如果按照上述方式定义，那么这两个头文件不可能包含在同一个程序中，因为String类会发生冲突。
所谓命名空间，是一种将程序库名称封装起来的方法，它就像在各个程序库中立起一道道围墙。比如：
// one.h
namespace one
{
    char func(char);
    class String { ... };
}

// somelib.h
namespace SomeLib
{
    class String { ... };
}

现在就算在同一个程序中使用String类也不会发生冲突了，因为他们分别变成了：one::String()以及Somelib::String()

这样，就可以通过声明命名空间来区分不同的类或函数等了。
比如C++标准库定义了命名空间：std，其中包含容器vector。

### 10. 堆栈   
栈指的是"堆栈",即stack:, first in last out，由编译器自动分配释放  
堆指的是"自由存储空间",即heap,用于在程序中动态分配的. 一般由程序员分配释放，若程序员不释放出现内存泄漏. 

### 11. 
STL 
容器：
所有的STL容器

容器（Container）的概念的出现早于模板（template），它原本是一个计算机科学领域中的一个重要概念，但在这里，它的概念和STL混合在一起了。下面是在STL中出现的7种容器：

vector（向量）——STL中标准而安全的数组。只能在vector 的“前面”增加数据。

deque（双端队列double-ended queue）——在功能上和vector相似，但是可以在前后两端向其中添加数据。 

list（列表）——游标一次只可以移动一步。如果你对链表已经很熟悉，那么STL中的list则是一个双向链表（每个节点有指向前驱和指向后继的两个指针）。

set（集合）——包含了经过排序了的数据，这些数据的值(value)必须是唯一的。

map（映射）——经过排序了的二元组的集合，map中的每个元素都是由两个值组成，其中的key（键值，一个map中的键值必须是唯一的）是在排序 或搜索时使用，它的值可以在容器中重新获取；而另一个值是该元素关联的数值。比如，除了可以ar[43] = "overripe"这样找到一个数据，map还可以通过ar["banana"] = "overripe"这样的方法找到一个数据。如果你想获得其中的元素信息，通过输入元素的全名就可以轻松实现。

multiset（多重集）——和集合（set）相似，然而其中的值不要求必须是唯一的（即可以有重复）。

multimap（多重映射）——和映射（map）相似，然而其中的键值不要求必须是唯一的（即可以有重复）。
注意：如果你阅读微软的帮助文档，你会遇到对每种容器的效率的陈述。比如：log(n*n)的插入时间。除非你要处理大量的数据，否则这些时间的影响是可 以忽略的。如果你发现你的程序有明显的滞后感或者需要处理时间攸关（time critical）的事情，你可以去了解更多有关各种容器运行效率的话题。

相当不错的blog：http://blog.csdn.net/wangshihui512/article/details/8867321 

### 12. 变量命名
CRect(int w_,int h_)//函数能修改private变量加_?
匈牙利命名法
Sometimes used as variable prefix for function arguments. When used with function arguments, it can represent in/out/inout:
_variable: Function input variable. More explicit: _inVariable
variable_: Function output variable. More explicit: outVariable_
_variable_: Input/output by function. More explicit: _inoutVariable_
http://www.yolinux.com/TUTORIALS/LinuxTutorialC++CodingStyle.html

### 13. java 和 C++ 区别

Java 的世界里不仅没有指针，更没有虚函数的概念，当然这个是针对使用者而言。且不说指针，虚函数的省略确实减轻了设计的负担，因为就C++的概念放到java里说，普通成员函数的重载都是虚函数重写，所以你只要对父类的某个方法重写了，以为着你达到了C++的虚函数效果了。所以说，Java还是蛮简单的，将一切复杂的东西抽取成简单的表面化。

### 14. C++优势之一
便于软件重用，体现于
(1)面向对象：继承，多态，标准类库
(2)泛型程序设计(generic programming)的思想：模板机制，以及标准模板库STL

### 15. 函数模板
bool operator()(const T& x,const T& y) const{}
后面那个const{}是什么意思？

如果一个函数这样定义：
那么，参数里的const表示传递给函数的值仅仅是x变量的一个复制，函数里不会修改x的值。
参数列表后面的const是用来修饰函数的，表示函数内部不会修改其他变量的值，也就是只读变量，不写变量。

### 16. struct的模板？ 貌似叫结构体模板
函数模板和结构体模板的区别：
区别是:struct默认的访问限制为public:,而class的访问限制为private. 

```
template<class T>
struct less:public binary_function<T,T,bool> //发现binary_function<T,T,bool>是从别的派生而来，不用管
{
	bool operator()(const T & x,const T & y){return x<y;} const;}; //const;不懂
}
```

类（以及类模板）的成员方法后缀写const 表示该方法在对象本身为const的时候也可以使用（意指该方法并不会修改对象本身的数据）。
你可以做一个实验，定义一个const类型的类对象，调用该对象的非const方法会出错，只能调用该对象的const方法

### 17. #include <valarray> 中对min,max的宏定义冲突了

1）在包含头文件valarray时使用下面的格式
#undef   min  
#undef   max  
include   <valarray>
using namespace std;

2)  如果要保留min,max宏，前后夹击  
  #pragma   push_macro("min")  
  #pragma   push_macro("max")  
  #undef   min  
  #undef   max  
  #include   <valarray>  
  using namespace std;
  #pragma   pop_macro("min")  
  #pragma   pop_macro("max")

### 18. #pragma怎么用？
现在还不太会
ref：http://blog.csdn.net/gueter/article/details/2234072 

### 19. 获得文件大小
(1)

```
test.seekg(0, std::ios::end);
size = test.tellg();
test.seekg(0);
```

(2)
Many systems provide the stat() and fstat() functions
which return a struct of info on the file; the struct
includes the file's size.

```
#include <sys\types.h> 
#include <sys\stat.h> 
int FileSize( const char * szFileName ) 
{ 
  struct stat fileStat; 
  int err = stat( szFileName, &fileStat ); 
  if (0 != err) return 0; 
  return fileStat.st_size; 
}
```

### 20. const_iterator和iterator
(1)iterator能通过这个p修改，p指向的内容。
(2)const_iterator不能。
ref:http://www.haogongju.net/art/1794712

### 21. 堆和栈的区别
在C++中，内存分成5个区，他们分别是堆、栈、自由存储区、全局/静态存储区和常量存储区。
(1)栈，就是那些由编译器在需要的时候分配，在不需要的时候自动清楚的变量的存储区。里面的变量通常是局部变量、函数参数等。
(2)堆，就是那些由new分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个new就要对应一个delete。如果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。
(3)自由存储区，就是那些由malloc等分配的内存块，他和堆是十分相似的，不过它是用free来结束自己的生命的。
(4)全局/静态存储区，全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量又分为初始化的和未初始化的，在C++里面没有这个区分了，他们共同占用同一块内存区。
(5)常量存储区，这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改（通过非正当手段也可以修改）

堆和栈的区别可以用如下的比喻来看出：    
(1)
使用栈就象我们去饭馆里吃饭，只管点菜（发出申请）、付钱、和吃（使用），吃饱了就  
走，不必理会切菜、洗菜等准备工作和洗碗、刷锅等扫尾工作，他的好处是快捷，但是自  
由度小。  
(2)
使用堆就象是自己动手做喜欢吃的菜肴，比较麻烦，但是比较符合自己的口味，而且自由  
度大。

### 22. 一些指针问题
(1)析构函数中有可能没注意将同一个区域释放了两次:
释放后应该立即把指针置NULL，这样就算再次释放也不会出错
(2)对于堆指针等，释放后，也最好设为NULL，同时释放前加if(NULL != p)等来判断

### 22. function overloading vs overriding
overload(函数重载):
same name which differ from each other in the type of the input and the output of the function.
同名不同参数不同返回值

overriding:
allows a subclass or child class to provide a specific implementation of a method 
that is already provided by one of its superclasses or parent classes.
Function in subclass has same name, same parameters or signature, and same return type as the method in the parent class.
虚函数的多态表现

### 23. C++ STL vector, valarray, 和bitSet使用方法对比

### 24. 抽象类(abstract type)
不能被直接实例化instantiated

### 25. const的问题
Declares 2 ints which are constant:
int const x1 = 3;
const int x2 = 3;

Declares a pointer whose data cannot be changed through the pointer:
const int *p = &someInt;

Declares a pointer who cannot be changed to point to something else:
int * const p = &someInt;

### 26. 进程与线程
http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html