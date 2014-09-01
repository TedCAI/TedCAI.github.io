---
layout: post
title: Practice on Programming笔记（第九周:STL2）
category: 笔记
description: STL2
tags: ["C++"]
---

### 关联容器(associative containers)
1. set,multiset,map,multimap
(1)内部元素有序排列，新元素插入的位置取决于它的值，查找速度快。
(2)除了个容器都有的函数外，还支持一下成员函数
find:查找等于某个值的元素(x<y和y<x都不成立为等于)
count:计算等于某个值的元素的个数(x<y和y<x都不成立为等于)
lower_bound/upper_bound:查找上下界
equal_range:同时查找上界和下界
insert:插入一个元素或一个区间

2. 预备知识：pair模板

```
template<class _T1,class _T2>
struct pair
{
	typedef _T1 first_type;
	typedef _T2 second_type;
	_T1 first;
	_T2 second;
	pair():first(),second(){}
	//无参构造函数1：
	//若first,second是成员对象，用无参构造函数来初始化
	//若first,second是基本类型变量，加圆括号也没有问题，也就是不初始化它了
	pair(const _T1 & __a,const _T2 & __b):first(__a),second(__b){}
	//构造函数2
	template<class _U1,class _U2>
	pair(const pair<_U1,_U2> & __p):first(__p.first),second(__p.second){}
	//构造函数3
	//是函数模板
};
```

(1)map/multimap容器里放着的都是pair模板类的对象，且按first从小到大排序
(2)第三个构造函数用法示例：

```
pair<int,int>
p(pair<double,double>(5.5,4.6));
//p.first=5,p.second=4
```

### multiset
1. 

```
template<class Key,class Pred=less<Key>>, //Pred大概是函数类？缺省值是less<Key>
		 class A = allocator<Key>>
class multiset
{...};
```

(1)Pred类型的变量决定了multiset中的元素，定义了“一个比另一个小”。
(2)运行过程中，会生成一个Pred类型的变量，假定为op,表达式op(x,y)返回true,x比y小(x排y前面)。
(3)less 模板的定义

```
template<class T>
struct less:public binary_function<T,T,bool> //结构体模板
{
	bool operator()(const T & x,const T & y){return x<y;} const; //const;不懂
	//类（以及类模板）的成员方法后缀写const 表示该方法在对象本身为const的时候也可以使用（意指该方法并不会修改对象本身的数据）。
}; 

```

2. multiset的成员函数
(1)find//查找val,<和>同时不成立  
(2)insert  
(3)count 
(4)lower_bound 找到一个最大的位置it,使得[begin(),it)中所有的元素都比val小。  
(5)upper_bound 与上面类似，使得[it,end())所有元素都比val大 
(6)find,insert,lower_bound,upper_bound复杂度O(logn)  
(7)equal_range:同时求得lower_bound和upper_bound,返回值是pair类模板的对象  

```
pair<iterator,iterator> equal_range(const T & val);
```

3. multiset的用法

```
#include <set>
using namespace std;
int main(){
	multiset<A> a;
	a.insert(A()); //error
}
```

为什么呢？
```
multiset<A> a; 等价于 multiset<A,less<A> > a;
插入元素时，multiset会将被插入元素和已有元素进行比较。
由于less模板是用<进行比较的，所以必须承载<
```

```
#include <iostream>
#include <set>//使用multiset
using namespace std;

template<class T>
void Print(T first, T last){
	for (;first!=last;++first) cout<<*first<<" ";
	cout <<endl;
}

class A{
private:
	int n;
public:
	A(int n_){n=n_;}
	friend bool operator<(const A & a1,const A & a2){return a1.n<a2.n;}
	friend ostream & operator<<(ostream & o,const A & a2){o<<a2.n; return o;}
	friend class MyLess;
};

struct MyLess{
	bool operator()(const A & a1,const A & a2){
	//按个位数比大小
		return(a1.n%10)<(a2.n%10);
	}
};

typedef multiset<A> MSET1;//MSET1用<比大小
typedef multiset<A,MyLess> MSET2;//MSET2用MyLess::operator()比大小

int main()
{
	const int SIZE = 6;
	A a[SIZE] = {4,22,19,8,33,40};
	MSET1 m1;
	m1.insert(a,a+SIZE);
	m1.insert(22);
	cout<<“1)”<<m1.count(22)<<endl;//输出1)2
	
	cout<<"2)";Print(m1.begin(),m1.end());//输出2)4 8 19 22 22 33 40
	MSET1::iterator pp = m1.find(19);
	if(pp!=m1.end())
		cout<<"found"<<endl;
		
	cout<<"3)";cout<< * m1.lower_bound(22)<<"," //查找位置it，使得[begin(),it)中所有元素都比val小
	<<* m1.upper_bound(22)<<endl;  //查找位置 [it,end())所有元素都比val大
	//输出3)22,33
	
	pp = m1.erase(m1.lower_bound(22),m1.upper_bound(22)); //删掉[22,22,33)
	//pp指向被删元素的下一个元素
	cout<<"4)";Print(m1.begin(),m1.end()); //输出 4) 4 8 19 33 40
	cout<<"5)";cout<<* pp <<endl;          //输出 5) 33
	
	MSET2 m2; // m2里的元素按n的个位数从小到大排
	m2.insert(a,a+SIZE);
	cout<< "6)";Print(m2.begin(),m2.end()); //输出6) 40 22 33 4 8 19
	
	return 0;
}
```

### set
1. set不能有重复元素 （重复元素a!<>b,a不小于b,a不大于b）

```
#include <iostream>
#include <set>
using namespace std;

int main(){
	typedef set<int>::iterator IT;
	int a[5] = {3,4,6,1,2};
	set<int> st(a,a+5);//st里是1 2 3 4 6
	pair<IT,bool> result;
	result = st.insert(5); //st变成 1 2 3 4 5 6
	if(result.second) //插入成功
		cout << * result.first << " inserted" << endl; //输出5 inserted
		
	if(st.insert(5).second) cout << * result.first << " inserted" << endl;
	else 
		cout<<*result.first<<"already exists" << endl; //输出 5 already exists
	pair<IT,IT> bounds = st.equal_range(4);
	cout<<* bounds.first << "," << * bounds.second; //输出： 4,5
	return 0;
}

```

### map/multimap
1. 预备知识：pair模板
2. multimap定义：

```
template<class Key,class T,class Pred=less<Key>, //Pred比大小的规则（函数）
		class A = allocator<T>>
class multimap{
	...
	typedef pair<const Key,T> value_type;
	...
};
//key代表关键字的类型
```

3. 一些概念
(1)multimap中的元素有<关键字，值>组成，每个元素是一个pair对象，关键字就是first成员变量，其类型是Key
(2)multimap中允许多个元素的关键字相同。元素按照first成员变量从小到大排列，缺省情况下用less<Key>定义关键字的“小于”关系。

4. 具体例子

```
#include <iostream>
#include <map>
using namespace std;
int main(){
	typedef multimap<int,double,less<int> > MMID;
	MMID pairs;
	cout<<"1)"<<pairs.count(15)<<endl;
	pairs.insert(MMID::value_type(15,2.7));//typedef pair<const Key,T> value_type;
	pairs.insert(MMID::value_type(15,99.3));
	cout<<"2)"<<pairs.cout(15)<<endl;//求关键字等于某值的元素个数
	pairs.insert(MMID::value_type(30,111.11));
	pairs.insert(MMID::value_type(10,22.22));
	
	for(MMID::const_iterator i=pairs.begin();i!=pairs.end();i++){
		cout<<"("<<i->first<<","<<i->second<<")"<<endl;
	}
} 
```

5. 学生成绩录入和查询系统

```
#include <iostream>
#include <map>
#include <string>
using namespace std;

class CStudent
{
	public:
		struct CInfo//类的内部还可以定义类
		{
			int id;
			string name;
		};
		int score;
		CInfo info;//学生的其他信息
}；

typedef multimap<int,CStudent::CInfo> MAP_STD;

int main(){
	MAP_STD mp;
	CStudent st;
	string cmd;
	while(cin>>cmd){
		if(cmd=="Add"){
			cin>>st.info.name>>st.info.id>>st.score;
			mp.insert(MAP_STD::value_type(st.score,st.info));
		}
		else if(cmd=="Query"){
			int score;
			cin>>score;
			MAP_STD::iterator p=mp.lower_bound(score); //iterator和const_iterator有什么区别？
			//lower_bound:查找一个最大的位置it,使得[begin(),it)中所有元素的first都比val小
			if(p!=mp.begin()){
				--p;
				score = p->first; //比要查询分数低的，最高分
				
				//然后要找到id最大的
				MAP_STD::iterator maxp = p;
				int maxId = p->second.id;
				for(;p!=mp.begin()&&p->first==score;--p){//遍历所有成绩和score相等的学生
					if(p->second.id>maxId){
						maxp = p;
						maxId = p->second.id;
					}
				}
				if(p->first==score){
				//如果上面循环是因为p==mp.begin()而终止
				//则p指向的元素还要处理
					if(p->second.id>maxId){
						maxp=p;
						maxId=p->second.id;
					}
				}
			cout<<maxp->second.name<<" "<<maxp->second.id<<" "<<maxp->first<<endl;
			}
			else
				//lower_bound的结果就是begin，说明没人分数比查询分数低
				cout<<"Nobody"<<endl;
		}
	}
	
	return 0;
}

```

*关联容器非常适合需要不断更新数据和查询数据，两个复杂度都是log(n)

6. extra
mp.insert(MAP_STD::value_type(st.score,st.info));
//也可以写成mp.insert(make_pair(st.score,st.info));
//make_pair返回的是模板类对象

7. map
map中的元素都是pair模板类对象。关键字(first成员变量)各不相同。
元素按照关键字从小到大排列，缺省情况下用less<Key>定义小于

```
template<class Key,class T,class Pred=less<Key>,
		class A = allocator<T> >
class map{
	...
	type_def pair <const Key,T> value_type;
	...
}		
```

8. map的[]成员函数
(1)若pairs为map模板类的对象，pairs[] 返回对关键字等于key的元素的值(second成员变量)的引用。
(2)若没有关键字为key的元素，则会往pairs里插入一个关键字为kay的元素，其值用无参构造函数初始化，并返回其值的引用。
(3)例子

```
map<int,double> pairs;
pairs[50]=5; //会修改pairs中关键字为50的元素，使其值变成5
//若不存在关键字等于50的元素，则插入此元素，并使其值变为5
```

9.map例子

```
#include <iostream>
#include <map>
using namespace std;
template <class Key,class Value>
ostream & operator<<(ostream & o,const pair<Key,Value> & p)
{
	o<<"("<<p.first<<","<<p.second<<")";
	return o;
}

int main(){

	typedef map<int,double,less<int> > MMID; //> >加空格的原因是防止编译器认为>>是右移运算符
	MMID pairs;
	cout<<"1)"<<pairs.cout(15)<<endl;
	
	pairs.insert(MMID::value_type(15.27));
	pairs.insert(make_pair(15,99.3)); //make_pair生成一个pair对象，这个插入不能成功
	//如果不成功，和set情况类似。
	cout<<"2)"<<pairs.count(15)<<endl;
	
	pairs.insert(MMID::value_type(20,9.3));
	MMID::iterator i;
	cout<<"3)";
	for(i=pairs.begin();i!=pairs.end();i++)
		cout<<*i<<",";
	cout<<endl;
	
	cout<<"4)";
	int n=pairs[40];//如果没有关键字为40的元素，则插入一个
	for(i=pairs.begin();i!=pairs.end();i++)
		cout<<*i<<",";
	cout<<endl;
	
	cout<<"5)";
	pairs[15]=6.28;//把关键字为15的元素值改成6.28
	for(i=pairs.begin();i!=pairs.end();i++)
		cout<<*i<<",";
	
}
``` 

输出结果：

```
1) 0
2) 1
3) (15,2.7),(20,9.3),
4) (15,2.7),(20,9.3),(40,0),
5) (15,6.28),(20,9.3),(40,0),
```