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
/*search*/

template <typename T> BinNodePosi(T) & BST<T>::search(const T & e)
{
	return searchIn(_root,e,_hot=NULL);	//start from root
}

static BinNodePosi(T) & searchIn(	//典型的尾递归(recursion),可改为迭代版(iteration)
	BinNodePosi(T) & v,		//当前(子)树根
	const T & e,			//目标关键码
	BinNodePosi(T) & hot)	//记忆热点,用了&引用变量,实际上就是对_hot的修改
{
	if( !v || (e == v->data) ) return v;	//失败或成功
	hot = v;	//记下当前(非空)节点
	(e< v->data)? v->lChild : v->rChild;
	return searchIn( v,e,hot );
}//运行时间正比于返回节点v的深度,不超过树高O(h)
```

2.查找(search)接口语义
(1)_hot 不管成功与否,都是指向命中节点,若失败,即可调用插入(insert)
(2)若e尚不存在,则:_hot作为新节点的父亲,v=search(e)为_hot对新孩子的引用

3.插入(insert)
(1)先借助search(e)确定插入位置及方向,再将新节点作为*叶子*插入

```
/*insert*/

template <typename T>
BinNodePosi(T) BST<T>::insert( const T & e ){
	BinNodePosi(T) & x = search(e);		//查找目标(留意_hot的设置)
	if(!x){
		x = new BinNode<T>(e,_hot);		//class内的函数可访问private
		_size++;
		updateHeightAbove(x);
	}
	
	return x;
}//复杂度O(h),在于updateHeightAbove和search,都不会超过O(h)
```

4.删除(delete)

```
/*delete*/

template <typename T> bool BST<T>::remove(const T & e){
	BinNodePosi(T) & x = search(e);
	if(!x) return false;
	
	removeAt(x,_hot);
	_size--;
	updateHeightAbove(_hot);
	return true;
}//复杂度O(h),在于updateHeightAbove和search,都不会超过O(h)
```

```
/*removeAt*/
//situation 1
//removeAt(x)的某一子树为空,则可将其替换为另一子树

template<typename T> 
static BinNodePosi(T) removeAt(BinNodePosi(T) & x, BinNodePosi(T) & hot){
	BinNodePosi(T) w = x;		//实际被摘除的节点,初值同x
	BinNodePosi(T) succ = NULL;	//实际被删除节点的接替者

	if		(!HasLChild(*x)) succ = x = x->rChild;	//左子树为空
	else if (!HasRChild(*x)) succ = x = x->lChild;	//右子树为空
	else {	/*situation 2: 左右子树并存,略微复杂*/ }
	
	hot = w->parent;
	if(succ) succ->parent = hot;
	release(w->data);
	release(w);
	return succ;
}
```

```
/*removeAt*/
//situation 2
//左右子树并存

//思路:直接后继,BinNode::succ(),返回当前节点在in order意义下的直接后继
//也就是在全树中不小于当前节点的最小的那个节点

//succ算法:
//在当前节点拥有右后代的情况下,首先进入对应右子树,然后在右子树沿着左侧分支不断下行到底,
//最终抵达的那个节点就是直接后继succ

template <typename T> static BinNodePosi(T)
removeAt(BinNodePosi(T) & x, BinNodePosi(T) & hot){
	/*...*/
	
	else {	/*situation 2: 左右子树并存,略微复杂*/ 
		w=w->succ();
		swap(x->data,w->data);
		BinNodePosi(T) u = w->parent;
		(u==x? u->rChild : u->lChild) = succ = w -> rChild;	//双向连接
		//两种情况,画图看
	}
	
	/*...*/
}
```

****BinNode::succ()这个在二叉树那个章节里没写实现过程吧!!

```
/*removeAt 完整实现*/

template<typename T> 
static BinNodePosi(T) removeAt(BinNodePosi(T) & x, BinNodePosi(T) & hot){
	BinNodePosi(T) w = x;		//实际被摘除的节点,初值同x
	BinNodePosi(T) succ = NULL;	//实际被删除节点的接替者

	if		(!HasLChild(*x)) succ = x = x->rChild;	//左子树为空
	else if (!HasRChild(*x)) succ = x = x->lChild;	//右子树为空
	else {	/*situation 2: 左右子树并存,略微复杂*/
		w=w->succ();
		swap(x->data,w->data);
		BinNodePosi(T) u = w->parent;
		(u==x? u->rChild : u->lChild) = succ = w -> rChild;	//双向连接
	}
	
	hot = w->parent;
	if(succ) succ->parent = hot;
	release(w->data);
	release(w);
	return succ;
}
```

### 平衡与等价
1.O(h)复杂度, 对h进行控制

2.随机生成法创建BST
(1)可能的生成序列n!种
(2)平均高度log(n)
(3)不同的关键码序列可能生成同一棵BST

3.随机组合
(1)所得BST总数恰好为Catalan(n)
(2)平均高度sqrt(n)

4.推广
(1)中位数,或者接近于中位数的关键码,尽早插入,BST的高度相应会降低

5.理想平衡
(1)节点数目固定时,兄弟子树高度接近平衡,全树倾向更低
(2)n个节点组成的二叉树,高度不低于logn,恰为logn,称作理想平衡(完全二叉树Complete Binary Tree;最好的是满二叉树Full Binary Tree)

6.适度平衡
(1)理想平衡出现概率极低,维护成本过高,适当放松标准
(2)渐近分析
(3)高度渐进地不超过O(logn),即可称作适度平衡
(4)适度平衡的BST,称作平衡二叉搜索树(Balanced Binary Search Tree,BBST)

7.歧义=等价
(1)歧义性:结构不同的BST,中序遍历序列可能相同
(2)上下可变:联接关系不尽相同,承袭关系可能颠倒
(3)左右不乱:中序遍历序列完全一致,全局单调非降

8.等价变换
(1)等价变换=旋转调整

### AVL树
1.AVL = BBST

2.重平衡(rebalance):令刚失衡的搜索树重新恢复为BBST的过程, 不超过O(logn)

3.平衡因子(balanced factor)
(1)对于二叉树中的任何一个节点v,都可以定义平衡因子(balanced factor)


资料:
1.卡塔兰数(Catalan)
http://zh.wikipedia.org/wiki/%E5%8D%A1%E5%A1%94%E5%85%B0%E6%95%B0