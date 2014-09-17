---
layout: post
title: 3DI面试的一道题
category: 笔记
description: FactSet面试
tags: ["C++","算法","数据结构"]
---

从Interview里也能学到一些新思想嗯. 3DI是一个3D搜索/打印的创业公司,我的毕设就是帮他们做的一个"Scan to search"的3D搜索系统,可惜的是他们很难提供签证...所以感觉没戏了

### 输出一个序列的powerset(所有子集)
1.用bit
因为毕设写rasterization的时候老玩bitset和01010的东西,所以这个方法感觉挺好,对int值的移位(shift)效率还是比较高的

```
#include <iostream>
using namespace std;

int main(){  
	const int size = 3;
	int S[size] = {1, 2, 3};    
	int total_sets = 1 << size;	//2^n

	for (int idx = 0; idx < total_sets; idx++){  
		int temp_idx = idx;  
		int element_id = 0;  
		cout<<"{";  
		while (temp_idx){  
			if (temp_idx & 1)	cout<<S[element_id];  
			temp_idx >>= 1;  
			++element_id;  
		}  
		cout<<"}"<<endl;  
	}  

	system("pause");
	return 0;  
}  
```

2.递归
面我的人说他的方法是递归,然后解释了一下,我记不太清他的方法了...网上又找了一个递归法

```
#include <iostream>
using namespace std;
void build(char *str,int *tag,int n)
{
	if(n==5)
	{
		cout<<"{";
		for(int i=0;i<5;i++)
			if(tag[i]==1)
				cout<<str[i];
		cout<<"}"<<endl;
		return;
	}
	tag[n] = 0;
	build(str,tag,n+1);
	tag[n] = 1;
	build(str,tag,n+1);
}
int main()
{
	char a[5]={'a','b','c','d','e'};
	int tag[5];
	build(a,tag,0);
	return 0;
}
```

ref:http://blog.csdn.net/qsyzb/article/details/23119529