---
layout: post
title: Practice on Programming笔记（第四周：运算符重载）
category: C++
description: 运算符重载
tags: ["C++"]
---

### 运算符重载 Operator overloading
1. 重载为普通函数
2. 重载为成员函数

### 赋值运算符重载
1. 赋值运算符两边类型不匹配，所以要重载
2. 只能重载为成员函数

3. 例子(string类)

```
class String{  
  private:  
    char *str;  
  public:  
    String(): str(NULL){} //构造函数，初始化str为NULL  
    const char * c_str(){return str;}  
    char * operator=(const char * s);  
    ~String();  
}  
char * String::operator=(const char * s){  
    if(str) delete [] str;  
    if(s){  //s不为NULL才执行copy  
        str = new char[strlen(s)+1];  
        strcpy(str,s);  
    }  
    else str = NULL;  
    return str;  
}  
String::~String(){  
    if(str) delete [] str;  
}  
int main(){  
    String s;  
    s = "Good Luck";  
    cout<<s.c_str()<<endl;  
    //String s2 = "hello"; 会出错, 是初始化语句，会调用构造函数  
    s = "Changed";  
    cout<<s.c_str()<<endl;  
    return 0;  
}  
```

4. 重载赋值运算符的意义-浅复制和深赋值  
(1)浅复制/浅拷贝  
执行逐个字节的复制工作（指针复制）  
指针问题（两个指针指向了同一个字串，删除的时候重复删除）  
(2)深复制/深拷贝  
将一个对象中指针变量指向的内容，复制到另一个对象中指针成员对象指向的地方  
添加成员函数  

```
String & operator=(const String &s){  
    if(str) delete [] str;  
    str=new char[strlen(s.str)+1];  
    strcpy(str,s.str);  
    return * this;  
}  
```

(3)s = s;//这时候会有问题
添加

```
if(str==s.str) return * this;
```

5. operator=返回值类型讨论
(1)返回void好不好？
考虑a=b=c;
a.operator=(b.operator=(c));
(2)返回String好不好？
为什么是String &
尽量保留运算符原本的特性
考虑(a=b)=c;

6. 复制构造函数
(1)会遇到同样的问题

```
String::String(String & s){
    if(s.str){    
        str = new char[strlen(s.str)+1];    
        strcpy(str,s.str);    
    }    
    else str = NULL;    
}  
```

7. 反正就是用引用&拷贝，而不是指针*拷贝咯
