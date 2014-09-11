---
layout: post
title: 图(graph)
category: 算法
description: 图
tags: ["C++","算法","数据结构"]
---

整理edX上Data Structures and Algorithms里所有排序算法.

### 冒泡排序
1.

### 归并排序
1.


### 选择排序(selection sort)

```
/*selection sort*/
//对列表中起始于位置p的连续n个元素做选择排序,valid(p) && rank(p) + n <= size

tamplate <typename T> void List<T>::selectionSort(Posi(T) p,int n){
	//待排区间(head,tail)
	Posi(T) head = p->pred;
	Posi(T) tail = p;
	for(int i=0; i<n; i++){ 	//head/tail可能是header/trailer.
		tail = tail->succ; 		//(tail到trailer的succ了怎么办? 岂不是null了?)	
		/*改进方法:
		if(tail->succ != NULL){
			tail = tail->succ;
		}
		*/
	}
	
	while(n>1){
		insertBefore(tail,remove( selectMax(head->succ,n) ));//insert要new,remove要delete
		tail = tail->pred;									 //是通常基本操作的100倍
		n--;												 //改进(1):直接修改reference
	}														 //改进(2):修改succ/pred
}
```

```
/*selectMax*/

template <typename T>
Posi(T) List<T>::selectMax(Posi(T) p, int n){
	Posi(T) max = p;
	
	for(Posi(T) cur=p;n>1;n--){
		if( !lt((cur = cur->succ)->data,max->data) ) //not less than
			max = cur;								 //painter's algorithm
	}												 //这里有一个trick, 要用>=,不用>
													 //selectMax()中对于多个相等的最大元素，选取其中位置最靠后者
	return max;
}
```

1. 分析:
(1)复杂度O(n^2)
(2)但是元素移动操作远少于起泡排序
(3)O(n^2)来自于元素*比较*
(4)第10章selectMax()可以在O(logn)而不是O(n)时间内完成

### 插入排序 (Insertion Sort)

1.将序列看成两部分
(1)sorted + unsorted

2.我的想法, 插入只需要对比O(logn)次,(binary compare?); 但是,在list里没办法call-by-rank

3.实现

```
/*Insertion Sort*/
//对列表中起始于位置p的连续n个元素做插入排序

template<typename T>
void List<T>::insertSort(Posi(T) p,int n){
	for(int r=0;r<n;r++){		//r是已排序的元素个数
		insertAfter( search(p->data,r,p),p->data );		
		p = p->succ;
		remove(p->pred);		//转向下一节点
	}//O(r+1)
}//仅用O(1)辅助空间,就地算法(in-place algorithm)
```

4.性能分析
(1)最好情况,几乎完全有序,O(n);最坏情况,O(n^2); 但平均也是O(n^2)
(2)selection所有情况都是O(n^2),没有最好最坏

5.后向分析法(backward analysis)
[0+1+...+(n-1)]/2+1 = O(n^2)

6.逆序对 (inversion)
(1)左大右小,构成inversion
(2)inversion记录在右(小)的那个元素
(3)逆序对数量可能n^2
(4)统计每一个元素所对应的inversion有多少个,累计能得到整个序列所含inversion的数目
(5)p所对应的inversion有多少个,就要经过多少次比较才能抵达最终插入位置
(6)最好情况,逆序对总数0,复杂度O(0+n)
(7)最坏情况,逆序对总数n^2,复杂度O(n^2+n)=O(n^2)