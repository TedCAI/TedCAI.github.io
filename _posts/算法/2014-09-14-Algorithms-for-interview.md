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
struct BinaryTree 
{
    int data;
    BinaryTree* left;
    BinaryTree* right;
};
 
BinaryTree* newTreeNode(int data)
{
    BinaryTree* newNode = new BinaryTree;
    newNode->data = data;
    newNode->left = NULL;
    newNode->right = NULL;
 
    return newNode;
}
 
int getIndex(int* arr, int val, int size)
{
    for(int i=0; i<size;i++) {
        if(arr[i] == val) 
            return i;
    }
    return -1;
}
  
BinaryTree* create_bintree(int* parray, int* iarray, int size)
{
    if(size == 0) return NULL;
 
    int rootVal = parray[0];
    BinaryTree* root  = newTreeNode(rootVal);
    int newIdx = getIndex(iarray, rootVal, size);
    root->left = create_bintree(parray+1, iarray, newIdx);
    root->right = create_bintree(parray+newIdx+1, iarray+newIdx+1, size-newIdx-1);
    return root;            
}

void main()
{
    int preorder[] = {7,10,4,3,1,2,8,11};
    int inorder[] = {4,10,3,1,7,11,8,2};
 
    BinaryTree* tree = create_bintree(preorder, inorder, 8);
}
```

ref:http://analgorithmaday.blogspot.co.uk/2011/05/construct-binary-tree-from-pre-order.html
ref:http://leetcode.com/2011/04/construct-binary-tree-from-inorder-and-preorder-postorder-traversal.html

