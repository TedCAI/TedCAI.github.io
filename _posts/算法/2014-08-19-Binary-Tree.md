---
layout: post
title: 二叉树(binary tree)
category: 算法
description: 二叉树
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我学习二叉树找到的一些有用的资料以及edX上Data Structures and Algorithms第四周的笔记.

### 二叉树(binary tree)
1. 二叉树定义
(1)由节点(node)的有限集合构成(节点度数不超过2)
(2)这个集合或为空集(empty),或为由一个根节点(root)及两颗互不相交,分别称作
这个根的左子树(left subtree)和右子树(right subtree)的二叉树组成的集合
(3)5种基本形态: 左右都不空,左空,右空,独根,空

2. 树概念
(1)depth:从root到node的距离
(2)height:从最深的leaf回到node的距离; empty tree的height是-1; 任何一点的height+depth<=全树的height
(3)rooted tree
(4)subtree
(5)child:孩子
(6)sibling:兄弟
(7)parent
(8)degree:节点r拥有的孩子的数目
(9)edge:边
edge = n - 1 		      (顶点总数-1)(任何一棵树中的边数,与其中顶点的数目是同阶的)
	 = sum(all(degree))   (所有顶点的度数之和)
(10)ordered tree: siblings are ordered and numbered
(11)root是所有node的公共ancestor, 深度为0
(12)没有后代的节点称作leaf

3. 树代码概念
(1)root()    		根节点
(2)parent()  		父节点
(3)firstChild()		长子
(4)nextSibling()	兄弟
(5)insert(i,e)		将e作为第i个孩子插入
(6)remove(i)		删除第i个孩子(及其后代)
(7)traverse()		遍历

4. 二叉树概念
(1)binary tree: node不超过2
(2)同一节点的孩子和子树,以左右区分: lChild(),rChild()
(3)隐含有序: 左在先,右在后
(4)depth为k,至多2^k个node
(5)含n个节点,高度为h的二叉树, h<n<2^(h+1)
(6)满二叉树(full binary tree): n = 2^(h+1) - 1
(7)真二叉树(true binary tree):每个node的degree都是even, 0或2
(8)通过二叉树描述多叉树: 
凡是有根且有序的树, 都可以通过二叉树描述表示:
长子兄弟法 firstChild,nextSibling 直接理解成二叉树 leftChild, rightChild

5. 二叉树实现

```
//BinNode模板类

#define BinNodePosi(T) BinNode<T> *  		//节点位置
template <typename T> 
struct BinNode{
	BinNodePosi(T) parent, lChild, rChild; 
	T data;
	int height; int size(); 						//高度, 子树规模
	BinNodePosi(T) insertAsLC(T const &); 			//作为左孩子插入新节点
	BinNodePosi(T) insertAsRC(T const &); 			//作为右孩子插入新节点
	BinNodePosi(T) succ();							//successor:(中序遍历意义下)当前节点的直接后继
	template <typename VST> void travLevel(VST &);	//子树层次遍历
	template <typename VST> void travPre(VST &);	//子树先序遍历
	template <typename VST> void travIn(VST &);		//子树中序遍历
	template <typename VST> void travPost(VST &);	//子树后序遍历
}

template <typename T>
BinNodePosi(T) BinNode<T>::insertAsLC(T const & e){
	return lChild = new BinNode(e,this); //data:e, parent:this
}

template <typename T>
BinNodePosi(T) BinNode<T>::insertAsRC(T const & e){
	return rChild = new BinNode(e,this); //data:e, parent:this
}

template <typename T>
int BinNode<T>::size(){				//后代中枢, 即以其为根的子树的规模
	int s = 1;						//计入本身
	if(lChild) s += lChild->size();	//递归计入左子树规模
	if(rChild) s += rChild->size();	//递归计入右子树规模
	return s;
}//O(n=|size|)
```

```
//BinTree 模板类

template <typename T> 
class BinTree{
protected:
	int _size;									//规模
	BinNodePosi(T) _root;						//根节点
	virtual int updateHeight(BinNodePosi(T) x);	//更新节点x的高度
	void updateHeightAbove(BinNodePosi(T) x);	//更新x及祖先的高度

public:
	int size() const {return _size;}
	bool empty() const {return !_root;}
	BinNodePosi(T) root() const {return _root;}
	/*子树接入,删除和分离接口*/
	/*遍历接口*/
}
```

```
//高度更新

#define stature(p) ((p)?(p)->height : -1)

//更新节点x高度, 具体规则因树不同而异
template <typename T>  
int BinTree<T>::updateHeight(BinNodePosi(T) x){
	return x->height = 1 +
	max(stature(x->lChild),stature(x->rChild));
}//采用常规二叉树规则, O(1)

//更新v及其历代祖先的高度
template <typename T>
void BinTree<T>::updateHeightAbove(BinNodePosi(T) x){
	while(x)	//可优化:一旦高度未变,即可终止
	{
		updateHeight(x);
		x = x->parent;
	}
}//O(n=depth(x))
```

```
//节点插入

template <typename T> BinNodePosi(T)
BinTree<T>::insertAsRC(BinNodePosi(T) x, T const & e){
	_size++; 
	x->insertAsRC(e);
	updateHeightAbove(x);	//x祖先的高度可能增加, 其余节点必然不变
	return x->rChild;
}
```

6. Tree traversal(遍历)
(1)按照某种次序访问树中各节点, 每个节点被访问恰好一次
(2)T = V U L U R (树 = 根U左子树U右子树)
(3)Pre-order(先序)	:  V|L|R 根|左子树|右子树
(4)In-order(中序)	:  L|V|R
(5)Post-order(后序)	:  L|R|V
(6)层次遍历9

7. 先序遍历 Preorder

```
//Preorder Recursion Version 先序遍历递归模式

template <typename T,typename VST>
void traverse(BinNodePosi(T) x, VST & visit){
	if(!x) return;
	visit(x->data);
	traverse(x->lChild, visit);
	traverse(x->rChild, visit);
} //T(n) = O(1)+T(a)+T(n-a-1) = O(n)

/*分析
递归的每一帧没有一个通用的格式,所以不能做到足够的小
虽然每一帧都是常数, 但常数的差异也非常大
所以改写成迭代形式可以更快

这里的递归都是尾递归, 非常容易化解为迭代形式, 只需要映入一个栈
*/
```

```
//Preorder Iteration Version 1 先序遍历迭代模式1

template <typename T,typename VST>
void travPre_I1(BinNodePosi(T) x, VST & visit){
	Stack <BinNodePosi(T)> S; //辅助栈
	if(x) S.push(x); //根节点入栈
	while(!S.empty()){
		x = S.pop(); visit(x->data);
		if( HasRChild(*x) ) S.push( x->rChild );	//FILO
		if( HasLChild(*x) ) S.push( x->lChild );	//LIFO
	}
}

/*分析
非常简明
然而不容易直接推广到inorder和postorder
所以要寻找其他等效的策略
(一直访问左孩子, 左孩子的左孩子, 这称为左侧链(left branch)
自上而下访问左侧链的节点, 再自下而上访问右侧链的节点
)
*/
```

```
//Preorder Iteration Version 2 先序遍历迭代模式2

template <typename T,typename VST> 	//分摊O(1)
static void visitAlongLeftBranch(BinNodePosi(T) x,VST & visit,
								 Stack<BinNodePosi(T)> & S)
{
	while(x) {
		visit( x->data );
		S.push( x->rChild );		//以后逆序出栈 LIFO
		x = x->lChild;				//沿左侧链(left branch)下行
	}
}

//主算法
template <typename T,typename VST>
void travPre_I2(BinNodePosi(T) x,VST & visit){
	Stack<BinNodePosi(T)> S;				//辅助栈
	while(true){
		visitAlongLeftBranch( x,visit,S ); 	//访问左侧链,右子树入栈缓冲
		if(S.empty()) break;
		x = S.pop();						//弹出下一子树的根
	}										//#pop = #push = #visit = O(n) = 分摊O(1)
}
```

8. 中序遍历 (in-order)

```
//Inorder Recursion Version 中序遍历递归模式

template <typename T,typename VST>
void traverse(BinNodePosi(T) x,VST & visit){
	if(!x) return;
	traverse(x->lChild,visit);
	visit(x->data);
	traverse(x->rChild,visit);
} //T(n) = T(a) + O(1) + T(n-a-1) = O(n)
```

```
//Inorder Iteration Version 中序遍历迭代模式

template <typename T>
static void goAlongLeftBranch( BinNodePosi(T) x,Stack<BinNodePosi(T)> & S )     //静态成员函数并不具体作用域某个对象,因此静态成员不需要通过对象就能访问
{
	while(x)	{S.push(x);  x = x->lChild;	}
}

template<typename T,typename V>
void travIn_I1(BinNodePosi(T) x, V & visit ){
	Stack<BinNodePosi(T)> S;
	while(true){
		goAlongLeftBranch(x,S);
		if(S.empty()) break;
		x = S.pop();  			//应该是x=S.top(); S.pop();
		visit(x->data);
		x = x->rChild; 			//转向右子树(可能为空,留意处理手法)
	}
}

/* 分析
while(true) 复杂度O(n); 隐含while(x)
但其实还是O(n)的复杂度
使用的是分摊+分析法, amortized+analysis
*/
```

9. 层次遍历(level-order)

```
//Level-order using queue 二叉树层次遍历

template <typename T,typename VST>
void BinNode<T>::travLevel(VST & visit){
	Queue<BinNodePosi(T)> Q;
	Q.enqueue(this); 					//根节点入队
	
	while(!Q.empty()){
		BinNodePosi(T) x = Q.dequeue();	//取出队首节点
		visit(x->data);
		if(HasLChild(*x))	Q.enqueue(x->lChild);
		if(HasRChild(*x))	Q.enqueue(x->rChild);
	
	}
}
```

10. 重构 (Reconstruct a binary tree)
(1)pre/post-order序列 + in-order序列 即可重构二叉树
(2)pre-order首位元素定位root,在in-order中可切分左子树和右子树,recursion重构整个二叉树

(3)对真二叉树A full binary tree or proper binary tree, pre-order + post-order 可重构二叉树
(4)pre-order找到root和left-subtree的root,post-order找到root和right-subtree的root

问题: 如果其中有两个元素一样, 要怎么定位?
条件: 二叉树结点值不能有相同的值

### 一些资料
1. 面试二叉树题目
http://blog.csdn.net/luckyxiaoqiang/article/details/7518888

2. 啊哈磊写的[坐在马桶上看算法] 二叉树, 讲解的挺好
http://www.cnblogs.com/ahalei/p/3738487.html

3. 不使用递归和栈中序遍历二叉树
http://www.acmerblog.com/inorder-tree-traversal-without-recursion-and-without-stack-5988.html