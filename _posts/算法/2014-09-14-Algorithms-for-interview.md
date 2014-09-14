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
Node *buildInorderPreorder(int in[], int pre[], int size, int offset) {
  if (size == 0) return NULL;
  
  int rootVal = pre[0];
  Node *root = new Node(rootVal);
  
  int lTree_size = mapIndex[rootVal]-offset;  // the divider's index
  root->left = buildInorderPreorder(in, pre+1, lTree_size, offset);
  root->right = buildInorderPreorder(in+lTree_size+1, pre+lTree_size+1, size-lTree_size-1, offset+lTree_size+1);
  return root;
}
```

ref:http://analgorithmaday.blogspot.co.uk/2011/05/construct-binary-tree-from-pre-order.html
ref:http://leetcode.com/2011/04/construct-binary-tree-from-inorder-and-preorder-postorder-traversal.html

