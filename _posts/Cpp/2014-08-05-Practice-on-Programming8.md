---
layout: post
title: Practice on Programming笔记（第八周:STL）
category: Cpp
description: STL
tags: ["C++"]
---

### STL概述

1. C++优势之一：便于软件重用
体现于
(1)面向对象：继承，多态，标准类库
(2)泛型程序设计(generic programming)的思想：模板机制，以及标准模板库STL

2. STL基本概念
(1)容器
可容纳各种数据类型的通用数据结构，是类模板
(2)迭代器
可用于依次存取容器中元素，类似于指针
(3)算法
用来操作容器中的元素的函数模板

3. 容器概述
可以用于存放各种类型的数据的数据结构，都是类模板
(1)顺序容器
vector,deque(double end queue),list(双向链表)
(2)关联容器
set,multiset,map,multimap
(3)容器适配器
stack,queue,priority_queue

*对象被插入容器中时，被插入的是对象的一个复制品，放入容器中对象所属的类，
往往还应该重载==和<运算符 (用于排序)

4. 顺序容器(sequence containers)
容器并非排序的，元素的插入位置同元素的值无关。

(1)vector
-动态数组，元素在内存连续存放。随机存取能在常数时间完成。在尾端增删元素具有较佳的性能(大部分情况下是常数时间);
-有时候需要O(N)的时间：
一般预先多分配存储空间，比如有10个元素，先分配32个元素的空间，可直接添加元素;
当加到33个元素的时候，存储空间不够，重新分配存储空间，把32个元素拷贝再增加，O(N)。
-在非尾部增删，O(N)

(2)deque
-双向队列，元素在内存连续存放。随机存取任何元素都能在常数时间完成（次于vector）
-两端增删元素有较佳性能（大部分情况是常数时间）
-到存储空间尾端再转回头端空闲空间的时候（判断是否越过尾端+倒回头端），比vector慢一些。

(3)list
-双向链表。元素在内存不连续存放。在任何位置增删元素都能在常数时间完成(前提是找到了那个元素的位置)。不支持随机存取。

5. 关联容器
-元素是排序的
-任何元素按相应排序规则确定位置
-查找时有非常好的性能
-通常以平衡二叉树方式实现，插入和检索时间都是O(log(N))

(1)set/multiset
set即集合。set中不允许相同元素，multiset允许存在相同元素。

(2)map/multimap
-map与set的不同在于map中存放的元素有且仅有两个成员变量，一个名为first，另一个名为second;
map根据first值对元素进行从小到大排序，并可快速根据first来检索元素。
-map与multimap的不同在于是否允许相同first值的元素。

*总之带multi的都是允许有相同元素的。

6. 容器适配器
(1)stack
LIFO
(2)queue
FIFO
(3)priority_queue
优先级队列。最高优先级元素总是第一个出列

7. 迭代器
(1)end返回指向容器中最后一个元素后面的位置的迭代器
(2)迭代器++操作，指向下一个元素
(3)i ！= v.end() //v.end()是最后一个元素后面

8. 双向迭代器
若p和p1都是双向迭代器
(1)++p,p++,--p,p-- 指向下/上一个元素
(2)*p 取p指向的元素
(3)p = p1 赋值
(4)p==p1,p!=p1 判断是否相等/不等

9. 随机访问迭代器
若p和p1都是随机访问迭代器
(1)双向迭代器的所有操作
(2)p+=i,p-=i 后/前移i个元素
(3)p+i,p-i 指向后/前的第i个元素的迭代器
(4)p[i] p后面的第[i]个元素的引用
(5)p<p1,p<=p1,p>p1,p>=p1 元素所在位置的比较

10. 容器迭代器类别
-vector/deque 随机访问
-list/set/multiset/map/multimap 双向
-stack/queue/priority_queue 不支持迭代器

11. vector/deque的随机迭代器
遍历
(1)for(int i=0;i<v.size();i++)
(2)for(int i=v.begin();i!=v.end();i++)
(3)for(int i=v.begin();i<v.end();i++)

12. list的双向迭代器
遍历
(1)for(list<int>::const_iterator i=v.begin();i!=v.end();++i)
*不能写i<v.end(),双向迭代器不支持<
*获得元素只能写*i，不能写v[i]

13. STL中的“相等”
(1)x==y 为真
(2)x<y||x>0为假 

14. 相等概念演示

```
#include <iostream>
#include <algorithm>
using namespace std;

class A{
	int v;
  public:
	A(int n):v(n){}
	bool operator<(const A & a2)const{
		cout<<v<<"<"<<a2.v<<"?"<<endl;
	}
	bool operator ==(const A & a2)const{
		cout<<v<<"=="<<a2.v<<"?"<<endl;
		return v == a2.v;
	}
}

int main(){
	A a[] = {A(1),A(2),A(3),A(4),A(5)};
	cout<<binary_search(a,a+4,A(9));//折半查找
	return 0;
}
```

输出结果：

```
3<9?
2<9?
1<9?  //false
9<1?  //false
1     //不大于，不小于，所以等于；没有调用==
```

### 顺序容器vector
1. 可变长的动态数组
2. 必须#include<vector>
3. 支持随机访问迭代器
-随机访问某元素时间为常数
-在尾部添加速度很快
-中间插入慢
4. 所有STL算法都能对vector操作

5. 二维动态数组
vector< vector<int> > v(3);
//v有三个元素，每个元素都是vector<int>容器

### list和deque
1. list容器：双向链表
(1)任何位置插入/删除：常数时间
(2)不能随机访问

2. sort函数
(1)list不支持完全随机访问，有自己的sort成员函数

3. deque容器
(1)双向队列
(2)#include<deque>
(3)所有适用于vector的都适用于deque
(4)还有push_front和pop_front

### 函数对象（function object）
1. 若一个类重载了运算符“()”,则该类的对象就成为函数对象(overload the brackets)
wiki:
-函数对象是一个程序设计的对象允许被当作普通函数来调用。
-函数对象与函数指针相比，有两个优点：第一是编译器可以内联执行函数对象的调用；第二是函数对象内部可以保持状态。
https://zh.wikipedia.org/wiki/函数指针
https://zh.wikipedia.org/wiki/函数对象

2. 例子

```
class CMyAverage{ //函数对象类 function object class
public:
	double operator()(int a1,int a2,int a3){
		return (double)(a1+a2+a3)/3;
	}
};

CMyAverage average;//function object
cout<<average(3,2,3);//average.operator()(3,2,3)
```

3. 函数对象的应用例子
(1)Dev C++中的Accumulate源代码1：

```
template<typename _InputIterator, typename _Tp>
_Tp accumulate(_InputIterator __first, _InputIterator __last,_Tp __init)
{
	for(;__first!=__last;++__first)
		__init = __init+*__first;
	return __init;
}
//typename 等价于class
```

(2)Dev C++中的Accumulate源代码2：

```
template<typename _InputIterator, typename _Tp,typename _BinaryOperation>
_Tp accumulate(_InputIterator __first, _InputIterator __last,_Tp __init,
			   _BinaryOperation __binary_op)
{
	for(;__first!=__last;++__first)
		__init =__binary_op( __init,*__first);
	return __init;
}
//调用accumulate时，和__binary_op对应的实参可以是个函数或函数对象
```

(3)函数对象的应用示例

```
#include <iostream>
#include <vector>
#include<algorithm>
#include<numeric>
#include<functional>
using namespace std;

int sumSquares(int total,int value)
{return total+value*value}

template<class T>
void PrintInterval(T first, T last)
{//输出区间[first,last)中的元素
	for(;first!=last;++first)
		cout<<*first<<" ";
	cout<<endl;
}

template<class T>
class SumPowers{
	private:
		int power;
	public:
		SumPowers(int p):power(p){}
		const T operator()(const T &total,const T & value)
		{//计算 value的power次方，加到total上
			T v = value;
			for(int i=0;i<power-1;++i)
				v = v*value;
			return total+v;
		}

}

int main()
{
const int SIZE = 10;
int a1[] = {1,2,3,4,5,6,7,8,9,10};
vector<int> v(a1,a1+SIZE);
cout<<"1)";PrintInterval(v.begin(),v.end());

int result = accumulate(v.begin(),v.end(),0,SumSquares);
cout<<"2)平方和："<<result<<endl;

result = accumulate(v.begin(),v.end(),0,SumPowers<int>(3));
//用到函数对象，SumPowers是类模板，SumPowers<int>是模板类，SumPowers<int>(3)函数对象
cout<<"3)立方和："<<result<<endl;

result = accumulate(v.begin(),v.end(),0,SumPowers<int>(4));
cout<<"4)4次方和："<<result;

return 0;
}
```

(4)看看SumSquares的accumulate的实例化

```
int result = accumulate(v.begin(),v.end(),0,SumSquares);

//实例化
int accumulate(vector<int>::iterator first,vector<int>::iterator last,
				int init,int (* op)(int,int) //函数指针
{
	for(;first!=last;++first)
		init=op(init,*first);
	return init;
}

```

(5)SumPowers的accumulate的实例化

```
accumulate(v.begin(),v.end(),0,SumPowers<int>(3));

//实例化
int accumulate(vector<int>::iterator first,vector<int>::iterator last,
				int init,SumPowers<int> op) //函数对象
{
	for(;first!=last;++first)
		init=op(init,*first);
	return init;
}
```

用处：不需要定义全局变量，用函数对象计算任意多次power的accumulate，程序局部性良好

3. STL中的函数对象类模板
(1)生成函数对象的模板：

```
#include <functional>
equal_to
greater
less
...
```

(2)greater函数对象类模板

```
template<class T>
struct greater:public binary_function<T,T,bool>{ //从别的派生而来
	bool operator()(const T& x,const T& y) const{
		return x>y;
	}
};
```

(3)greater的应用(于算法中)
list有两个sort成员函数
-

```
void sort();
```

将list中的元素按"<"升序排列

-

```
template <class Compare>
void sort(Compare op);
```

将list中的元素按op规定的比较方法升序排列。
op(x,y)返回值为true认为x小于y(自己的理解：x排在y前面，即为数组[x,y]这样的顺序)。

(4)写出MyMax模板

```
#include <iostream>
#include <iterator>
using namespace std;

class MyLess{
  public:
	bool operator()(int a1,int a2){
		if((a1%10)<(a2%10))	return true;
		else     			return false;
	}
};

bool MyCompare(int a1,int a2){
	if((a1%10)<(a2%10)) return false;
	else        		return true;
};

int main()
{
	int a[]={35,7,13,19,12};
	cout<<* MyMax(a,a+5,MyLess())<<endl;
	cout<<* MyMax(a,a+5,MyCompare)<<endl;
	return 0;
}
```

输出：19 12

怎么写MyMax的模板呢？

```
template <class T,class Pred>
T MyMax(T first,T last,Pred myless)
{
	T tmpMax = first;
	for(;first!=last;++first)
		if(myless(*tmpMax,*first))
			tmpMax=first;
	return tmpMax;
}
```