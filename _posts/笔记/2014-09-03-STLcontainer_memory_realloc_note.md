---
layout: post
title: C++ STL 容器预分配空间的笔记
category: 笔记
description: C++ STL 容器预分配空间的笔记
tags: ["C++"]
---

面试的时候的一个问题, 用哪种数据结构储存从客户端发来的request, 然后每返回服务器端一个request从容器中删除该request. 一开始回答的是用vector, 但是发现vector会预分配内存空间, pop掉元素的时候仍然会占用空间. 后来回答说list. 面试的工程师说deque应该也可以. 但我不清楚deque会不会预分配一段空间. 

所以就查了一下: 

deque双向队列情况比较特殊，deque将内存分块，每次分配固定大小的分块，一个分块填充满后开辟新的分块，也属于散布-连续混杂的情况，虽然deque会预分配内存空间，但也不会产生realloc（人家是alloc），所以也不具有capacity属性。


##原文内容: [C++][STL]容器的capacity、max_size以及内存分配

capacity - 容器的成员函数capacity()取得

max_size - 容器的成员函数max_size()取得

STL容器的capacity属性，表示STL在发生realloc前能允许的最大元素数，也可以理解为预分配的内存空间。例如一个vector<int> v的capacity为5，当插入第6个元素时，vector会realloc，vector内部数据会复制到另外一个内存区域。这样之前指向vector中的元素的指针、迭代器等等均会失效。

max_size属性和capacity不同，表示STL容器允许的最大元素数，通常，这个数是一个很大的常整数，可以理解为无穷大。这个数目与平台和实现相关，在我的机器上vector<int>的max_size为1073741823，而string的max_size为4294967294。因为max_size很大~所以基本不会发生元素数超过max_size的情况，只需知道两者区别即可。

##设定capacity
###List, Map/Multimap, Set/Multiset, Deque
并不是所有的容器都会发生realloc，List，Map/Multimap，Set/Multiset的元素在内存中散布，不预分配内存，所以不会产生realloc的情况，对于这些容器，其capacity是无意义的，所以这些容器没有capacity()成员函数，也没有capacity属性。

deque双向队列情况比较特殊，deque将内存分块，每次分配固定大小的分块，一个分块填充满后开辟新的分块，也属于散布-连续混杂的情况，虽然deque会预分配内存空间，但也不会产生realloc（人家是alloc），所以也不具有capacity属性。

###Vector, String, basic_string<wchar_t>
实际具有capacity属性的容器只有vector和string，在不同实现下，capacity也不尽相同。在我的机器上，情况如下：

vector<T>

默认构造函数 - capacity = 0

使用构造函数vector<T>(n, value=T())指定capacity - capacity = n

string  （basic_string<char> ）

默认构造函数 - capacity = 15

没有指定capacity的构造式

指定了初始字符串的 - capacity = 大于字符串长度且等于n*15-1

 basic_string<wchar_t>

默认构造函数 - capacity = 7

指定了初始字符串的 - capacity = 大于字符串长度且等于n*8-1

 

除了通过构造式设定capacity，也可以使用reserve(n)来设定容器对象的capacity，避免之后的realloc。不过这一过程是只增不减的，如果n小于当前capacity，则reserve(n)无效。

 

##收缩容器的内存占用空间
###Vector, String, basic_string<wchar_t>
对于vector和string，因为reserve()的效果是只增不减，所以无法通过reserve()来收缩空间。如需收缩内存空间，思路如下：

新建一个vector/string

为新建的vector/string填充原容器的内容

将原容器的变量名指向新的容器

实际使用中只需要一条语句即可。

```
vector<int> v(10, 5);
v.reserve(20);
vector<int>(v).swap(v);
```
	
reference:http://www.cnblogs.com/SelaSelah/archive/2012/05/30/2526736.html