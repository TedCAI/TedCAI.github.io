---
layout: post
title: 动态规划(Dynamic Programming)笔记
category: 算法
description: 动态规划
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我学习动态规划找到的一些有用的资料以及edX上清华Data Structures第一章的笔记.

### Fibonacci
1. 递归(Recursion)

```
int fib(int n){
	return (n<2)?n:(fib(n-1)_fib(n-2));
}
//复杂度:O(2^n)
//空间:O(n)
```

2. 记忆(memoization)
(1)将已计算过实例的结果制表备查
(2)ref:http://en.wikipedia.org/wiki/Memoization

3. 动态规划(Dynamic Programming)
(1)颠倒计算方向:由自顶而下递归,为自底而上迭代

```
int fib(int n){
	int f=0; //fib(0)
	int g=1; //fib(1)
	while(0<n--){
		g=g+f;
		f=g-f;
	}
	return g;
}
//复杂度:O(n)
//空间:O(1)
```

```
//自己感觉更易懂的写法
int fib(int n){
	int f=0; //fib(0)
	int g=1; //fib(1)
	while(n-- > 0){
		f=g;
		g=g+f;
	}
	return g;
}
//复杂度:O(n)
//空间:O(1)
```

### 最长公共子序列(Longest common subsequence problem,LCS)
1. 子序列(subsequence):由序列中若干字符,按原相对次序构成
e.g.
tsinghua子序列sina, computer子序列cute

2. 最长公共子序列(Longest common subsequence):两个序列公共子序列中的最长者
e.g.
edudational 和 advantage: data/dana (可能有多个)
didactical 和 advantage: data (可能有歧义,第一个词did...)

3. 递归(recursion)解法
对于序列A[0,n]和B[0,m]LCS(A,B),三种情况
(1)n=-1或者m=-1, 
取作空序列("")											//可作为递归基(base)
(2)A[n]='X'=B[m],
取作LCS(A[0,n-1],B[0,m-1])+'X'							//减而治之decrease and concour
(3)A[n]!=B[m],
在LCS(A[0,n],B[0,m-1])和LCS(A[0,n-1],B[0,m])取更长者	//分而治之devide and concour

分析:
最好情况(不出现(2)的情况),只需要O(n+m)
出现(2)的情况,就像Fibonacci那样出现雷同,当n=m时,复杂度O(2^n)
子问题总共不过O(n+m)种
所以采用dynamic programming策略, 只需O(n*m)时间

4. Dynamic Programming解法  
(1)制表  
(2)分而治之,取左上+1  
(3)减而治之,看上,左,取最大的复制  

![Demo](https://github.com/mincongzhang/mincongzhang.github.io/tree/master/_posts/算法/LCS.jpg)

!(https://github.com/mincongzhang/mincongzhang.github.io/tree/master/_posts/算法/LCS.jpg)[/LCS.jpg]

![Demo](https://github.com/mincongzhang/mincongzhang.github.io/raw/master/tree/_posts/算法/LCS.jpg)
