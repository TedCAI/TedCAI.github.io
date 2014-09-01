---
layout: post
title: Perl学习笔记
category: 脚本
description: Perl学习笔记
tags: ["perl"]
---



### @ARGV
http://zhidao.baidu.com/question/129677073.html
含义是包含了程序从命令行得到的所有参数。

### Perl子程序sub
http://www.jb51.net/article/12785.htm

### Perl中use和require用法对比
http://developer.51cto.com/art/201007/213710.htm
本文和大家重点学习一下Perl use和require用法对比，这两个函数都是一个意思,加载和引用Perl的模块,或者是子程序，区别在于Perl use是在当前默认的里面去寻找,一旦模块不在指定的区域内的化,用Perl use是不可以引入的。

### perl中my与local的区别
http://blog.sina.com.cn/s/blog_4fcdb9280100l46v.html
perl学习笔记1---local与my用法
      在函数定义中，使用my关键字来确保用户定义函数中所使用的变量名不与任何其它同名的全局变量冲突，它使其后面的变量成为通常语言所谓的local变量。缺省情况下，perl函数中的变量是全局变量。 local变量先save再store
$x = 9;
sub mm{
    print "$x\n";
}
sub gg_my{
    my $x = 11;
    print "call-my $x\n";
    mm;
}
sub gg_local {
    local $x = 20;
    print "call-local $x\n";
    mm;
}
gg_my;
gg_local;
print $x;
输出结果是：
call_my 11
9
call_local 20
20
9
可以看出，local函数在创建全局变量的一个局部实例后即可将它传递给任何子例程并继续进行局部初始赋值。运算符my以与local函数同样的方法创建一次局部实例，然而，由my创建的局部实例不能传递给其它子例程，传递给子例程进行运算的将是全局值而非局部引用值。
当locla所在的子例程结束后，全局变量的值又恢复为初始定义值。

### perl File::Basename模块的使用
http://www.perlcn.com/perlbc/perljj/346.html

### perl IO::File模块的使用
http://www.perlcn.com/perlbc/perljj/351.html

$::test->description("40PGIGE block is selected:");
$: 是字符设置,此后的字符串将被分开,以填充连续的字段. ->是调用类或者对象的方法 $::test是 $main::test的缩写，表示main包里的$test对象，然后->箭头符号是调用$test对象的方法

use QFT::COMMON qw(checkError);
导入checkError这个方法

http://bbs.chinaunix.net/thread-880218-1-1.html
use CGI qw();不导入任何符号及符号集
use CGI qw(:all);导入所有方法
use CGI qw(:standard);导入standard这个方法集合
use CGI;导入默认的方法,可能是什么也没有
use CGI qw(redirect);导入redirect这个方法


### perl--last和next指令
http://hi.baidu.com/study_cs/item/7137df5154a22dd8d48bace2
last 退出循环陈述
next 到循环的下一个陈述

### $::
http://zhidao.baidu.com/question/509252041.html

### Exec()
http://perldoc.perl.org/functions/exec.html