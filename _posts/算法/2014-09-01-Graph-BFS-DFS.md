---
layout: post
title: 图(graph):广度优先(BFS)和深度优先(DFS)
category: 算法
description: 图
tags: ["C++","算法","数据结构"]
---

广度优先(BFS)和深度优先(DFS)

### 广度优先搜索(Breadth First Search)
1.化繁为简,非线性结构转化为线性结构(遍历序列)
2.图->半线性结构树(支撑树)->转化为树的算法问题->线性结构
3.体现为针对某种目标的查找过程:搜索(search)

4.搜索策略
(1)始自顶点s的广度优先算法(Breadth First Search)
(2)访问顶点s
(3)依次访问s所有尚未访问的邻接顶点
(4)依次访问它们尚未访问的邻接顶点
(5)直到没有尚未访问的邻接顶点
(6)结果是一个极大的无环图(一棵树)
(7)这棵树涵盖了原图所有的顶点,所以称为支撑树(spanning tree)
(8)图的广度优先遍历实际上就等同于树的层次遍历

5.实现

```
/*Graph::BFS()*/

template <typename Tv,typename Te>	//顶点类型, 边类型
void Graph<Tv,Te>::BFS( int init_v,int & clock ){
	Queue<int> Q;				//利用队列,和树的层次遍历(level order)类似
	status(init_v) = DISCOVERED;
	Q.enqueue(init_v);			//入队; 
	
	while(!Q.empty()){
		int v = Q.dequeue();
		dTime(v) = ++clock; 	//时间标签,给出时间进度,
		
		for(int u = firstNbr(v);-1<u;u=nextNbr(v,u)){
			/*视u的状态,分别处理,符合状态的就入队*/
			if(status(u)==UNDISCOVERED){
				status(u) = DISCOVERED;
				Q.enqueue(u);
				status(v,u) = TREE;		//引入树边(UNDETERMINED->TREE)
				parent(u) = v;
			} else {
				status(v,u) = CROSS;	//将(v,u)归类于跨边
			}
		}//其实不明白status和parent是怎么玩的?
		
		status(v) = VISITED;	//至此,当前顶点访问完毕
		//每一个顶点的状态都会由最初的undiscovered转化为discovered并最终转化为visited
		
	}
}
```

6.多连通
(1)图有可能包含多个连通域

```
template <typename Tv,typename Te>
void Graph<Tv,Te>::bfs(int s){
	reset(); int clock=0; int v = s;//初始化
	
	do
		if( UNDISCOVERED == status(v) )
			BFS(v,clock);
	while( s!=(v=(++v%n)) );	//这里不明白......
	//按序号访问,不漏不重
	
}//无论共有多少连通/可达分量
```

7.复杂度
(1)访问neighbour是O(n^2),因为每个vertex都要走一遍它的neighbour判断是否符合条件(邻接矩阵)
(2)neighbour是紧凑的整体,高速缓冲机制(在后面的B树中会提及),储存级别的巨大速度差异,抵消O(n^2)成O(n)
(3)邻接矩阵改成邻接表(list),可以直接成O(n)

8.最短路径问题(shortest path)
(1)考虑vertex到root的shortest path
(2)考虑树的层次遍历(level order traversal of tree)
(3)最短通路就是图BFS树的vertex到root的通路

### 深度优先搜索(Depth First Search)
1.相比BFS,DFS策略更为简明,过程更为复杂,功能更强大

2.伪码(psudocode)

```
DFS(s) //始自顶点s的DFS
{
	访问顶点s
	若s尚有*未被访问*的neighbour,则任取其一u,*递归*执行DFS(u)
	否则,返回
}
```

3.代码框架

```
/*Graph::DFS()*/

template <typename Tv,typename Te>	//顶点类型,边类型
void Graph<Tv,Te>::DFS(int v,int & clock){

	dTime(v) = ++clock;			//开始时间?
	status(v) = DISCOVERED; 	//发现当前顶点v
	
	for( int u=firstNbr(v),-1<u,u=nextNbr(v,u) ){
		/*...视u的状态,分别处理...*/
		/*...与BFS不同,含有递归...*/
	
		status(v) = VISITED;
		fTime = ++clock;		//节点访问完毕,结束时间?
		//至此,当前顶点v访问完毕
	}
	
}
```

4.代码细节

```
/*Graph::DFS()*/

template <typename Tv,typename Te>	//顶点类型,边类型
void Graph<Tv,Te>::DFS(int v,int & clock){

	dTime(v) = ++clock;			//开始时间?
	status(v) = DISCOVERED; 	//发现当前顶点v
	
	for( int u=firstNbr(v),-1<u,u=nextNbr(v,u) ){
		/*...视u的状态,分别处理...*/
		/*...与BFS不同,含有递归...*/
		switch( status(u) ){
			//u尚未发现,支撑树可再次拓展
			case UNDISCOVERED:	
				status(v,u) = TREE;
				parent(u) = v;
				DFS(u,clock);
			break;
			
			//u已被发现但尚未访问,应属被后代指向的祖先
			case DISCOVERED:
				status(v,u) = BACKWARD;//回向边(回边)
			break;
			
			//u已访问完毕(VISITED,有向图),则视承袭关系分为前向边或跨边
			default:
				status(v,u) = dTime(v)<dTime(u)? FORWARD:CROSS;//看谁更早被发现
			break;
		}//switch
	
		status(v) = VISITED;
		fTime = ++clock;		//节点访问完毕,结束时间?
		//至此,当前顶点v访问完毕
	}
	
}
```

5.D-4无向图