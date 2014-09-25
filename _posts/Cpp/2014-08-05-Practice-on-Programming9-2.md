---
layout: post
title: Practice on Programming笔记（第九周:STL3）
category: Cpp
description: 容器适配器和STL算法
tags: ["C++"]
---

### 容器适配器(container Adaptor)
1. 可以用某种顺序容器来实现
(让已有的顺序容器以栈/队列的方式工作)

(1)stack(栈)：后进先出(LIFO)
头文件<stack>

(2)queue(队列):先进先出(FIFO)
头文件<queue>

(3)priority_queue(优先级队列):最高优先级元素总是第一个出列
头文件<queue>

2. 成员函数
(1)push:添加一个元素
(2)top:返回栈顶或队头元素的引用
(3)pop:删除一个元素
(4)容器适配器上没有迭代器:STL中各种排序，查找，变序等算法都不适用

3. stack
(1)LIFO
(2)只能插入，删除，访问栈顶
(3)可用vector,list,deque来实现
-缺省情况下，用deque实现
-用vector和deque实现比用list实现性能好
(4)成员函数

```
void push(const T &x);
void pop();
T & top();//可读取栈顶元素，也可修改栈顶元素
```

```
template<class T,Cont = deque<T> >
class stack{
...
};
```

4. queue
(1)和stack基本类似，可以用list和deque实现
(2)缺省情况下用deque实现
(3)成员函数push,pop,top
-push发生在队尾
-pop,top发生在队头，FIFO

```
template<class T,class Cont =deque<T> >
class queue{
...
};
```

5. priority_queue
(1)和queue类似，可以用vector和deque实现
(2)缺省情况下用vector
(3)通常用*堆排序*实现，保证最大的元素总是在最前面
-执行pop删除的是最大的元素
-执行top返回的是最大元素的引用(但是不允许修改队头元素)
(4)默认的比较器是less<T>

*适用于不停的从元素中取最大值的情况

6. 例子

```
#include <queue>
#include <iostream>
using namespace std;
int main(){
	priority_queue<double> priorities;
	priorities.push(3.2);
	priorities.push(9.8);
	priorities.push(5.4);
	while(!priorities.empty()){
		cout<<priorities.top()<<" ";
		priorities.pop();
	}
	return 0;
}//输出:9.8 5.4 3.2
```

### STL算法
1. 大致分七类：
(1)不变序列算法
(2)变值算法
(3)删除算法
(4)变序算法
(5)排序算法
(6)有序区间算法
(7)数值算法(bitset)

2. 大多重载的算法都是有两个版本的
(1)用"=="判断元素是否相等，或用"<"来比较大小
(2)多出一个类型参数"Pred"和函数形参"Pred op(函数名字或者函数对象)": 
通过表达式"op(x,y)"的返回值true/false,判断x是否"等于"y,或者x是否"小于"y
(3)例子

```
iterator min_element(iterator first,iterator last);
iterator min_element(iterator first,iterator last, Pred op);
```

### 七类算法
1. 不变序列算法
(1)不修改作用的容器或对象
(2)适用于顺序容器(sequential container)和关联容器(associative container)
(3)复杂度都是O(n)
(4)例子

```
min//两者最小值
max
min_element//求区间最小值
max_element
for_each//定义一个fun对每个element操作,但不修改其值
find//查找等于某值的元素,比较重要
...
...
```

```
#include <iostream>
#include <algorithm>

class A{
public:
	int n;
	A(int i):n(i){}
};
bool operator<(const A &a1,const A & a2){
	cout<<"called"<<endl;
	if(a1.n==3 && a2.n==7)
		return true;
	return false;
}
//只有3<7是true

int main(){
	A aa[] = {3,5,7,2,1};
	cout<<min_element(aa,aa+5)->n<<endl;//都用了重载的小于符号
	cout<<max_element(aa,aa+5)->n<<endl;//都用了重载的小于符号
}
```

```
输出结果:
<called
...
...
3
<called
...
...
7
```

2. 变值算法
(1)会修改源区间或目标区间元素的值
(2)值被修改的那个区间,不可属于关联容器(associative container)(那就是不能应用于关联容器呗?)
(3)例子

```
for_each // 对每个元素做操作,可以修改值
copy //复制区间,有一种奇怪用法后面会说
copy_backward //从后往前copy,为什么呢,在有重叠的区间,顺序copy可能会把前一个区间后面的元素覆盖了,所以要从后往前copy
transform // 将一个区间元素变形后拷贝到另一个区间
swap_ranges //交换两个区间内容
fill //用某个值填充区间
generate //用某个操作的结果填充区间
```

```
#include <vector>
#include <iostream>
#include <numeric> // ?
#include <list>
#include <algorithm>
#include <iterator>
using namespace std;
class CLessThan9{ //判断是不是小于9
	public:
	bool operator()(int n){return n<9;}
};
void outputSquare(int value){cout <<value*value<" ";}
int calculateCube(int value){return value*value*value;}

int main(){
	const int SIZE = 10;
	int a1[] = {1,2,3,4,5,6,7,8,9,10};
	int a2[] = {100,2,8,1,50,3,8,9,10,2};
	vector<int> v(a1,a1+SIZE);
	random_shuffle(v.begin(),v.end()); //伪随机打乱
	
	cout<<endl<<"1)";
	ostream_iterator<int> output(cout," "); //实例化(realize)为int,定义output为输出,输出的东西都是int类型,每输出一个都带空格 
	copy(v.begin(),v.end(),output); //随机洗牌输出,copy的神奇用法

	cout<<endl<<"2)";
	copy(a2,a2+SIZE,v.begin()); //保证v(目标区间)里有足够空间,要不然数组越界
	cout<<count(v.begin(),v.end(),8); //输出 2
	
	cout<<endl<<"3)";
	cout<<count_if(v.begin(),v.end(),CLessThan9()); //输出 6

	cout<<endl<<"4)";
	cout<<*(min_element(v.begin(),v.end()));
	
	cout<<endl<<"5)";
	cout<<*(max_element(v.begin(),v.end()));
	
	cout<<endl<<"6)";
	cout<<accumulate(v.begin(),v.end(),0);//求和,起始为0	
	
	cout<<endl<<"7)";
	for_each(v.begin(),v.end(),outputSquare);
	
	cout<<endl<<"8)";
	vector<int> cubes(SIZE);
	transform(a1,a1+SIZE,cubes.begin(),calculateCube);
	copy(cubes.begin(),cubes.end(),output); //只是得瑟一下copy的用法
	
	return 0;
	}
```

copy函数模板(算法)
(1)

```
ostream_iterator<int> output(cout," ");
//定义了一个对象可以通过cout输出以" "(空格)分隔的一个一个整数
copy(v.begin(),v.end(),output);
//使得v的内容在cout上输出
```

(2)

```
template<class InIt,class OutIt>
OutIt copy(InIt first,InIt last,OutIt x);
//本函数对每个在区间[0,last-first)中的N执行一次 *(x+N)=*(first+N),返回x+N

copy(v.begin(),v.end(),output);
//first和last的类型是 vector<int>::const_iterator
//output的类型是ostream_iterator<int>
```

(3)copy源代码

```
template<class _II,class _OI>
inline _OI copy(_II _F,_II _L,_OI_X)
{
	for(;_F!=_L;++_X,++_F)
		*_X=*_F;
	return(_X);
}
```

(4)ostream_iterator

```
#include <iostream>
#include <fstream>
#include <string>
#include <algorithm>
#include <iterator>
using namespace std;
int main(){
	int a[4]={1,2,3,4};
	My_ostream_iterator<int> oit(cout,"*");
	copy(a,a+4,oit);//输出:1*2*3*4*
	
	ofstream oFile("test.txt",ios::out);//打开文件
	My_ostream_iterator<int> oitf(oFile,"*");
	copy(a,a+4,oitf); //向test.txt文件中写入1*2*3*4*
	oFile.close();
	return 0;
}//如何编写My_ostream_iterator? 比较难
```

调用copy(a,a+4,oitf)实例化后

```
My_ostream_iterator copy(int* _F,int* _L,My_ostream_iterator<int> _X)
{
	for(;_F!=_L;++_X,++_F)
		*_X=*_F;
	return(_X);
} //++_X的++运算符要重载,*_X的*要重载,*_X=*_F的=也要重载
```

自己写出ostream_iterator

```
#include <iterator>
template<class T>
class My_ostream_iterator : public iterator<output_iterator_tag,T> //编译器里的更复杂,不需要管这个
{
private:
	string sep;//分隔符
	ostream & os; //无参构造函数是私有的,只能用引用
public:
	My_ostream_iterator(ostream & o,string s):sep(s),os(o){}
	void operator ++(){};//++只需要有定义即可,不需要做什么
	My_ostream_iterator & operator*(){return *this;}
	My_ostream_iterator & operator=(const T & val)
	{os<<val<<sep; return *this;}
};
```

3. 删除算法
(1)删除一个容器里的某些元素
(2)不会使容器里的元素减少
-将所有应该被删除的元素看作空位置
-用留下的元素从后往前移,依次去填空位置
-元素往前移后,它原来的位置也算空位置,被后面的元素补上
-最后没有被补上的空位置,维持其原来的值不变
(3)不应作用于关联容器
(4)复杂度都是O(n)
(5)例子

```
remove
remove_if
remove_copy //copy区间到另一个区间,等于某个值的元素不copy
remove_copy_if //copy区间到另一个区间,符合某种条件的元素不copy
unique //重要, 删除区间中连续相等的元素, 只留下一个, 可自定义比较器
unique_copy //拷贝区间到另一个区间,连续相等的元素只拷贝第一个到目标区间,可自定义比较器
```

Unique:

```
//用==比较是否相等
template<class FwdIt>
FwdIt unique(FwdIt first,FwdIt last);

//用比较器,返回true说明相等
template<class FwdIt, class Pred>
FwdIt unique(FwdIt first,FwdIt second, Pred pr);

//对[first,last)这个序列中连续相等的元素,只留下第一个
//返回值是迭代器,指向元素删除后的区间的最后一个有效元素的后面
```

例子:

```
int main(){
	int a[5]={1,2,3,2,5};
	int b[6]={1,2,3,2,5,6};
	ostream_iterator<int> oit(cout,",");
	//如数组等结构其元素无法删除,所以算作空位置然后填充,返回有效区间后一元素的指针
	
	int * p = remove(a,a+5,2);
	cout<<"1)"; copy(a,a+5,oit);cout<<endl;
	//输出1)1,3,5,2,5
	
	cout<<"2)"<<p-a<<endl;
	//输出2) 3
	
	vector<int> v(b,b+6);
	remove(v.begin(),v.end(),2);
	cout<<"3)"; copy(v.begin(),v.end(),oit); cout<<endl;
	//输出3) 1,3,5,6,5,6,
	
	cout<<"4)"; cout<<v.size()<<endl;
	//v中的元素没有减少,输出4) 6
	
	return 0;
}
```

4. 变序算法
(1)改变容器中元素的顺序
(2)但是不改变元素的值
(3)不适用于关联容器
(4)复杂度O(n)
(5)例子

```
reverse           //颠倒区间前后次序
reverse_copy      //把颠倒后的区间拷贝到另一个区间,源区间不变
rotate 		      //将区间进行循环左移  //编程珠玑里有一个手相转换算法感觉不错?
rotate_copy       //将区间以首尾相接的形式进行旋转后的结果拷贝到另一个区间,源区间不变
next_permutation  //将区间改为下一个排列(可自定义比较器)?  (全排列)
prev_permutation  //将区间改为上一个排列(可自定义比较器)?  (全排列)
random_shuffle    //随机打乱区间内元素的顺序
partition         //把区间内满足某个条件的元素移到前面, 不满足的移到后面
stable_partition  //同上, 但对两部分元素保持原来顺序
```

next_permutation例子

```
#include <iostream>
#include <algorithm>
#include <string>
#include <list>
#include <iterator>
using namespace std;

int main(){
	string str = "231";
	char szStr[] = "324";
	while(next_permutation(str.begin(),str.end())){
		cout<<str<<endl;
	}
	
	cout<<"***"<<endl;
	
	while(next_permutation(szStr,szStr+3)){
		cout<<szStr<<endl;
	}
	
	cout<<"***"<<endl;
	
	int a[]={8,7,10};
	list<int> Is(a,a+3);
	while(next_permutation(Is.begin(),Is.end())){
		list<int>::iterator i;
		for(i=Is.begin();i!=Is.end();++i)
			cout<<*i<<" ";
		cout<<endl;
	}
}
```

5. 排序算法
(1)复杂度更高,一般是O(nlog(n))
(2)排序算法需要随机访问迭代器的支持
(3)不适用于关联容器和list
(4)例子

```
//以下都可自定义比较器

sort 				//从小到大排序
stable_sort			//同上,并保持相等元素间的相对次序
partial_sort		//部分排序,直到最小的n个元素就位
partial_sort_copy	//将前n个元素的排序结果拷贝到别处,源区间不变
nth_element			//对区间部分排序,使得第n小的元素(n从0开始算)就位,而且比它小的都在前面,大的都在后面

//还有堆排序
make_heap			//使区间成为一个"堆"
push_heap			//将元素加入一个"堆"区间
pop_heap			//删除堆顶元素
sort_heap			//堆区间进行排序,排序结束后盖区间就是普通有序区间,不再是"堆"了
```

sort例子

```
#include <iostream>
#include <algorithm>
using namespace std;
class MyLess{
	public:
	bool operator()(int n1,int n2){
		return(n1%10)<(n2%10);
	}
};

int main(){
	int a[]={14,2,9,111,78};
	sort(a,a+5,MyLess());
	for(int i=0;i<5;i++)
		cout<<a[i]<<" ";
	cout<<endl;
	//输出: 111 2 14 78 9
	
	sort(a,a+5,greater<int>());
	for(int i=0;i<5;i++)
		cout<<a[i]<<" ";
	//输出: 111 78 14 9 2
}
```

评论:
(1)sort是快排,复杂度O(nlog(n))
-平均性能最优
-最坏的情况下性能非常差O(n^2)
(2)stable_sort是归并排序
-能保持相等元素之间的先后次序
-有足够储存空间的情况下,复杂度O(nlog(n)); 否则O(n*log(n)*log(n))
(3)排序算法要求随机存取迭代器, list不能使用排序算法,要使用list::sort

6. 有序区间算法
(1)要求所操作的区间是已经从小到大排好序的
(2)需要随机访问迭代器
(3)不能用去关联容器和list
(4)例子

```
binary_search	//判断区间中是否包含某个功能; O(log(n))
includes		//判断是否一个区间中的每个元素都在另一个区间中; O(x*log(n)),x是要找的元素的个数
lower_bound		// O(log(n))
upper_bound		// O(log(n))
equal_range		// O(log(n))
merge			//合并两个有序区间到第三个区间; O(n)

//集合操作算法
set_union					//两个有序区间的并集拷贝到第三个区间
set_intersection			//同上,交集
set_difference				//两个有序区间的差拷贝到第三个区间 (集合A-B)
set_symmetric_difference	//同上,对称差(A并B-A交B)
set_intersection			//交集
inplace_merge				//将两个连续的有序区间原地合并为一个有序区间
```

```
#include <vector>
#include <bitset>
#include <iostream>
#include <numeric>
#include <list>
#include <algorithm>
using namespace std;

bool Greater10(int n)
{
	return n>10;
}

int main(){
	const int SIZE = 10;
	int a1[]={2,8,1,50,3,100,8,9,10,2};
	vector<int> v(a1,a1+SIZE);
	ostream_iterator<int> output(cout," ");
	vector<int>::iterator location;
	
	location = find(v.begin(),v.end(),10);
	if(location!=v.end()){
		cout<<"1)"<<location-v.begin()<<endl; 
	}
	//输出 8
	
	location = find_if(v.begin(),v.end(),Greater10);
	if(location!=v.end()){
		cout<<"2)"<<location-v.begin()<<endl;
	}
	//输出 3
	
	sort(v.begin(),v.end());
	if(binary_search(v.begin(),v.end(),9)){
		cout<<"3)"<<"9 found";
	}
	//输出9 found
}
```

bitset

```
template<size_t N>  
//size_t:bitset的size操作返回bitset对象中二进制位中的个数，返回值类型是size_t
class bitset
{
	......
}

bitset<40> bst;
```

(1)N是个整型常数
(2)bst是一个由40位组成
(3)用bitset的函数可以方便的访问任何一位
