---
layout: post
title: 为面试所准备的算法练习
category: 算法
description: 算法练习
tags: ["C++","算法","数据结构"]
---

### 反转double linked list
1.新建list,从后往前copy
2.两个指针,一前一后,swap (假如有header和trailer是最快的,没有trailer的话要遍历2次)
3.每个节点交换pred,succ (没有trailer是最快的)

```
/* reverse double linked list:交换pred,succ */

#define P(T) ListNode<T> *

void List<T>::reverse(List<T> list){
	P(T) p = list.header;
	P(T) temp;

	while(p->succ != null){
		temp = p->succ;
		p->succ = p->pred;
		p->pred = temp;
		p = p->pred;
	}//break when p == old trailer
	
	temp = p->succ;
	p->succ = p->pred;
	p->pred = temp;
	
	list.trailer = list.header;
	list.header = p;
}
```

ref:http://www.codeproject.com/Articles/27742/How-To-Reverse-a-Linked-List-Different-Ways


### Serialization/Deserialization of a Binary Tree 

```
/*Serialization*/

void writeBinaryTree(BinaryTree *p, ostream &out) {
  if (!p) {
    out << "# ";
  } else {
    out << p->data << " ";
    writeBinaryTree(p->left, out);
    writeBinaryTree(p->right, out);
  }
}
```

```
/*Deserialization*/

void readBinaryTree(BinaryTree *p, ifstream &fin) {
  int token;
  bool isNumber;
  if (!readNextToken(token, fin, isNumber)) 
    return;
  if (isNumber) {
    p = new BinaryTree(token);
    readBinaryTree(p->left, fin);
    readBinaryTree(p->right, fin);
  }
}
```

ref:http://leetcode.com/2010/09/serializationdeserialization-of-binary.html
ref:http://leetcode.com/2010/09/saving-binary-search-tree-to-file.html

### Reconstruct a Binary Tree given the preorder and inorder traversal orders

```
const int MAX = 256;
// a fast lookup for inorder's element -> index
// binary tree's element must be in the range of [0, MAX-1]
int mapIndex[MAX];
void mapToIndices(int inorder[], int n) {
  for (int i = 0; i < n; i++) {
    mapIndex[inorder[i]] = i;
  }
}
 
// precondition: mapToIndices must be called before entry
Node * buildInorderPreorder(int in[], int pre[], int size, int offset) {
  if (size == 0) return NULL;
  
  int rootVal = pre[0];
  Node *root = new Node(rootVal);
  
  int lTree_size = mapIndex[rootVal]-offset;  // the divider's index,key step!
  root->left = buildInorderPreorder(in, pre+1, lTree_size, offset);
  root->right = buildInorderPreorder(in+lTree_size+1, pre+lTree_size+1, size-lTree_size-1, offset+lTree_size+1);
  return root;
}
```

### FactSet Software development Interview
从FactSet的Interview里也能学到一些新思想哈哈

面试流程(感觉不记下来过两天就忘了亏了):
1. 第一轮(还是个中国工程师,正式面试之前都和我说中文,还介绍说每周都可以有个电脑游戏时间和同事们一起玩):
一个工程师介绍他们的最近的产品,以及公司理念, 软件职位干的活, 我问了他们软件开发流程, 是SDE+QA,不玩agile开发. QA丢给印度做,因为便宜.

FactSet其他部门会获取很多数据,然后稍微处理再丢给软件开发部门处理,包括NPL等处理手法,最后再通过终端显示给客户. 数据都非常严谨有reference(每项数据都有其来源引用最后源头是原公司的数据/财务报表之类的?). 

数据大概有每天各大公司的财务状况,以及其他银行和金融公司对这个公司的分析数据,客户可以定制专门看哪些银行(公司)对这个公司的分析, 或者把没用的扔黑名单. 公司可以通过自己的定制情报,然后自己再分析, 从而得出有价值的情报, 自己用. 

这些终端一方面有技术壁垒,各个公司自己也会抬高自己的使用难度, 从而增加用户黏性, 防止用户选用别人的终端. (如彭博社的彭博机). 而且公司终端还会提供各种细致的服务,从订票到订酒店都有...只是为了让这些富翁用户爽...

2. 第二轮:
(1)询问本科毕设,讲解了算法和使用的数据库,悲剧的是我忘记我的数据库用的是SQLite而不是PostgreSQL,被反问尴尬了一下...面试前还是得熟悉自己的简历

(2)问我想要面C++还是Java; 我选C++
-给一段二叉(搜索)树的代码,解释代码
-让我说这段代码有什么能改进的地方, 我发现destructor是空着的,他们问不写destructor会发生什么? 我说会占用内存,垃圾碎片清不掉.野指针.
-让我说怎么写destructor: basic idea是从叶子remove到根部, 可以写个recursion. 然后我正准备写, 他们说不是有个遍历函数么可以利用. 我说哦那就直接把cout改成delete就行了.
-然后我想不到别改进了,他们提示说这个二叉树只能玩int,我说哦那能写个模板类template T.
-然后他们说写模板类会有什么问题, 我说可能T类没有重载>,<,==符号会出问题
-然后再让我找问题,我说find函数只是返回true或者false不太好, 可以返回找到的节点的指针. 但是他们说这是二叉(搜索)树,有序的, 你找到之后插入别的奇怪节点就破坏了结构.
-提示说是不是少了一个remove的成员函数,然后我说是的,然后问我怎么remove
-第一个简单的remove是节点没有后代,直接remove; 然后remove的节点处在中间, 我记得刷题的时候这个问题容易解决但是当时忘了. 然后我说子树可以重构一下, 然后我又说子树太大重构的话神复杂. 后来突然想到可以先swap要remove的节点到右子树的最左叶,然后remove那个左叶就ok了.他们说brilliant非常好.

-然后给我一串数字让我按代码把树建出来,很简单
-然后问我遍历函数是怎么遍历的,我记得是postorder,然后他们说怎么写preorder和inorder, 用递归很容易写. (但是我已经累了老记不清preorder和inorder的顺序)
-让我解释怎么样递归遍历, 把preorder遍历的结果写出来. 写的有点磕巴

-然后问怎么serialization和deserialization,这时已经累了我说有preorder和inorder就可以重建. 说preorder能找到root,然后inorder能找到左右子树,递归重建. 我又想到leetcode的那个deserialization,加#号一个preorder就搞定. 结果傻逼了发现原来这不是普通二叉树是已经排序好的,直接有个preorder就可以完成重建. 被吐槽说internet lies.

-二叉树系列终于完结,轮到下一个面试
-给了一页脚本,不知道是什么语言. 让我看一遍告诉他们这个脚本干了啥, 这脚本略奇葩各种GOTO, 看完发现就根据输入算了一个a^b; 解释了一下,并不难. 
-然后问我这个脚本可能出现的问题:我说你输入的是非数字就悲剧了. 然后他们提醒说b还有可能是负数, 根据脚本b是负数的话这个循环没得完了. 他们说怎么改进, 我说加一个判断b是不是负数就完事了. 然后相应的改改代码计算1/(a^b).
-然后若b是负数还有个问题就是这结果是1/(a^b),a不能是0, a是0分母就是0就傻逼了. 他们貌似没想到这个问题? 觉得我说的有道理

-然后又给了我这种脚本语言的另一页代码,有改动但发现这运行结果还是a^b,但是调用了函数计算, 还有recursion(之前的脚本没有函数,直接一路算下去). 
-我以为有陷阱仔细看了发现没问题结果还是a^b. 

-他们说这两个脚本哪个好, 我说当然是第一个, 第二个你丫递归又麻烦还容易stack overflow, 递归栈还占内存, 他们说好的确实是. 然后给了另一页纸是运行结果,递归的结果果然是栈溢出...他们说这是他们自己内部用的脚本语言,叫TCL script貌似...第二轮貌似就结束了.

3. 第三轮:HR和技术部门的经理面试
-HR问了一些为什么我学电信又学vision最后来他们公司找software, 我吹牛说从我的毕设我发现algorithm很重要, 然后master各种学术, 发现你会学术不会写代码也废了,所以我还是想在basic software development发展. 
-HR又问你平常怎么干活的,我说weekday普通干活, weekend可能出去玩或者看online course.
-HR又问有没有小组作业,我说有的,我小组作业和hackathon玩的都是extreme programming,两个人一起写, 一个人不ok了另一个人接替,他们说这略恐怖,在他们那工作不会这么累, 这种方式绝对是definitely NOPE
-然后技术经理问我爱立信的PT router是啥, 我说可以用网页配环境升级, 不需要把路由器拿下来; 他说这路由器是我们用的那个? 我说不是, 这PT router虽然是叫mini link,但是还是超级巨大的; 他们说这真讽刺哈哈...
-然后又胡扯了一堆,他们问我有啥问题, 我说我中饭后喜欢打个盹,结果回来发现我说错了,打盹是(take a nap),但是我说成take a snap(打个折), 他们表示不知道是啥, 我说就是睡一觉...他们说哈哈随便...
-然后就寒暄一下就完事了, 他们说你现在可以回去打个折了(take a snap)...我说是的嗯....

ref:http://analgorithmaday.blogspot.co.uk/2011/05/construct-binary-tree-from-pre-order.html
ref:http://leetcode.com/2011/04/construct-binary-tree-from-inorder-and-preorder-postorder-traversal.html

