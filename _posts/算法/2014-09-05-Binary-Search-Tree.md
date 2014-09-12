---
layout: post
title: 二叉搜索树(Binary search tree)
category: 算法
description: 链表
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我在edX上Data Structures and Algorithms学习二叉搜索树的笔记.

### 二叉搜索树(Binary search tree)一些概念
1.结合Vector和List的优势
(1)(二叉树)形式的二维
(2)有序
(3)平衡二叉搜索树(Balanced Binary Search Tree)(BBST)

2.数据项的访问方式:循关键码访问(call by key)
(1)每一个数据项拥有关键码(key),以此为特征互相区分
(2)关键码之间: 大小比较;相等比对(判断是否完全一致)
(3)数据集合中的数据项,统一表示和实现为词条(Entry)形式

3.词条(Entry)

```
/*Entry*/

template<typename K,typename V> 
struct Entry{
	K key; 		//关键码
	V value;	//数值(其他信息)
				//也称作<key,value> pair
	
	Entry( K k=K(),V v = V() )   : key(k),value(v){};			//默认constructor
	Entry( Entry<K,V> const & e ): key(e.key),value(e.value){}; //克隆
	
	//比较器,判等器(从此,不必严格区分词条及其对应的关键码)
	bool operator< (Entry<K,V> const & e){return key < e.key;}
	bool operator> (Entry<K,V> const & e){return key > e.key;}
	bool operator==(Entry<K,V> const & e){return key ==e.key;}
	bool operator!=(Entry<K,V> const & e){return key !=e.key;}
}
```

4.Binary Search Tree(BST)
(1)节点(node)-词条(entry)-关键码(key) 等同

(2)顺序性 (任一node): 
-大于等于左*后代*(L_subtree<= V)
-小于等于右*后代*(V <=R_subtree)
-L_subtree <= V <= R_subtree

(3)顺序性导出全局特征:单调性
-BST的中序遍历(in order)序列,必然单调非降
-是BST的充要条件

(4)因此,微观上满足顺序性,宏观上满足单调性

5.接口

```
/*BST template class*/

template <typename T> class BST: public BinTree<T>{	//derived from BinTree
  public:											//以virtual修饰,强制要求后续派生类重写
	virtual BinNodePosi(T) & search(const T &);	//查找
	virtual BinNodePosi(T)   insert(const T &); //插入
	virtual  bool			 remove(const T &); //删除

  protected://共用操作接口
	BinNodePosi(T) _hot;  		//命中节点的父亲
	
	BinNodePosi(T) connect34(	//3+4重构
	BinNodePosi(T),BinNodePosi(T),BinNodePosi(T),
	BinNodePosi(T),BinNodePosi(T),BinNodePosi(T),BinNodePosi(T));
	
	BinNodePosi(T) rotateAt( BinNodePosi(T) );	//旋转调整
}
```

### 二叉搜索树(Binary search tree)算法及实现
1.查找(search)

```
template <typename T> BinNodePosi(T) & BST<T>::search(const T & e)
{
	return searchIn(_root,e,_hot=NULL);	//start from root
}

static BinNodePosi(T) & searchIn(	//典型的尾递归(recursion),可改为迭代版(iteration)
	BinNodePosi(T) & v,		//当前(子)树根
	const T & e,			//目标关键码
	BinNodePosi(T) & hot)	//记忆热点
{
	if( !v || (e == v->data) ) return v;	//失败或成功
	hot = v;	//记下当前(非空)节点
	(e< v->data)? v->lChild : v->rChild;
	return searchIn( v,e,hot );
}//运行时间正比于返回节点v的深度,不超过树高O(h)
```