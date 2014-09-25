---
layout: post
title: Practice on Programming笔记（第十一/二周:枚举/递归）
category: Cpp
description: 枚举和递归
tags: ["C++","算法"]
---

### 枚举
1. 基于已有知识进行答案猜测

2. 例子:求小于N的最大素数(prime number)
(1)枚举, N-1是素数? N-2是素数? ...N-K是素数
(2)N-K是素数的充要条件: N-K不能被任何一个大于1,小于N-K的素数整除
(3)转化为求小于N-K的全部素数

3. 解决方法
(1)2是素数,记为PRIM(0)
(2)根据PRIM(0),PRIM(1),...,PRIM(k),寻找比PRIM(k)大的最小素数PRIM(k+1)
(3)如果PRIM(k+1)大于N,则PRIM(k)是我需要找的素数

### 递归
1. 函数直接或间接的调用自身
2. 调用栈
注意:函数的局部变量是存在栈上的, 如果有体积大的局部变量,比如数组,
而递归层次可能很深,也许会导致栈溢出
解决:可以考虑使用全局数组或动态分配数组

### 用栈替代递归
1. 汉诺塔问题递归解法

```
#include <iostream>
using namespace std;
void Hanoi(int n, char src,char mid,char dest){
	if(n==1){
		cout<<src<<"->"<<dest<<endl;
		return;
	}
	
	Hanoi(n-1,src,dest,mid);
	cout<<src<<"->"<<dest<<endl;
	Hanoi(n-1,mid,src,dest);
	return;
}
```

2. 汉诺塔问题非递归解法(栈FILO)

```
#include <iostream>
#include <stack>
using namespace std;
struct Problem{
	int n;
	char src,mid,dest;
	Problem(int nn,char s,char m,char d):n(nn),src(s),mid(m),desk(d){}
};//一个problem变量代表一个子问题,将src上的n个盘子,以mid为中介,移动到desk

stack<Problem> stk; //若有n个盘子,则栈的高度不超过n*3

int main(){
	int n; cin>>n;
	stk.push(Problem(n,'A','B','C'));;
	
	while(!stk.empty()){
		Problem curPrb = stk.top();
		stk.pop();
		
		if(curPrb.n==1) cout<<curPrb.src<<"->"<<curPrb.desk<<endl;
		else{
			stk.push(Problem(curPrb.n-1,curPrb.mid,curPrb.src,curPrb.desk));
			//B->C
			stk.push(Problem(1,curPrb.src,curPrb.mid,curPrb.desk));
			//1 A->C
			stk.push(Problem(curPrb.n-1,curPrb.src,curPrb.desk,curPrb.mid));
			//A->B, 最后入栈的子问题先被处理
		}
	}
	
	return 0;
}
```

3. 分析递归解法
(1)编译器生成的代码自动维护一个问题的栈. 栈里每个字问题的描述中多了一个项:返回地址
(2)返回地址可以描述该字问题已经解决到哪个步骤了
(3)递归函数里的参数和局部变量,都是放在栈顶上的