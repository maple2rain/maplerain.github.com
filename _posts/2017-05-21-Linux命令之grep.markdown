---
layout: post
author: LPF
title: Linux命令之grep
date: 2017-05-21 20:11:17
updated: 2017-05-21 23:15:02
tags:
- Linux Command
categories:
- Study
- Computer
- OS
- Linux
- Command
---
> 对于经常用电脑的人来说，恐怕最常做的事情就是查找了吧！不管是查找文件，还是查找文件文本中的数据，如果不好好掌握查找的技巧的话都不能高效地工作。因此，本文重点介绍Linux下一个高级的选取工具——grep

----------


# 选取包含特定模式的行

`grep`程序 从标准输入或者文件中读取数据，抽取所有包含特定模式的行，并写到标准输出。
特定模式，可以是特定的字符串，更可以是正则表达式。因此，可以好好学习以下正则表达式。
`grep`程序的语法为：

```sh
$ grep [option] pattern [file...]
```

其中，*option*是选项，*pattern*是搜索模式，*file*是输入文件的名称。

比如，大多数Unix系统将每个用户标识的基本信息保存在一个名为*/etc/passwd*的文件中，每个用户标识对应一行信息。为了显示某用户标识的信息，可以使用`grep`在这个文件中搜索该模式。例如，为了显示用户标识为**maple**的有关信息，可以使用命令：

```sh
$ grep maple /etc/passwd
```

当指定包含标点符号或者特殊字符的模式时，应该将他们引用，从而使shell能够正确地解释命令。例如，如果要在文件*info*中搜索冒号后跟一个空格的模式，可以使用命令：

```sh
$ grep ': ' info
```

----------


> 名称含义
**grep**
在20世纪70年代初期，最初版本的Unix所使用的文本编辑器是**ed**。在**ed**中，有一个命令可以用来在文件中搜索所有包含一个指定模式行，然后在终端上打印这些行。
这个命令就是**g**，代表*global*，因为他能够搜索整个文件。当使用**g**打印包含某个模式的所有行时，语法为：
**g/** re **/p**
其中**g**代表*global*,*re*就是描述希望搜索的模式的正则表达式，**p**代表print。
**grep**的名称就是来自这个偶然产生的缩写。

----------

# 最重要的选项

`grep`有许多选项，但是我们只讨论最重要的选项。他们基本上是最常用到的几个选项。

|选项|内容|含义|
|---|
|-c|count|显示所抽取行的数量，而不是所抽取行的本身|
|-i|ignore|忽略大小写之间的区别，用于匹配大小写无关的数据|
|-n|number|用于在输出的每一行前面加一个相对行号|
|-l|list filename|当不止搜索一个文件时，显示包含搜索模式的文件名，而不是内容|
|-L||与-l选项相反|
|-w|word|匹配完整的单词，如搜索*now*时，显示*now*，不显示*know*|
|-v|reverse|选取不包含指定模式的所有行|
|-x|line|整行匹配，完全和搜索模式相同的行|
|-r|recursive|递归搜索整个目录树|
|-s|suppress|不输出错误信息|
 
 
 
----------


# 兄弟过滤器：look

尽管`grep`很灵活，很高效，但是也相对比较复杂。相比之下，`lool`简单且快速。`look`程序搜索**以字母顺序排列的数据，并查找所有以特定模式开头的行。**

`look`程序的使用方式有两种：

1. 使用一个或多个文件中的有序数据
2. 搜索字典文件

当`look`搜索一个或多个文件时，语法为：

```sh
$ look [-df] pattern file
```

比如，现在有一个学生评价文价文件*evaluation*，文件开头是评价等级，后接学生姓名。数据以字母顺序排列。例如：

```sh
A William
C Peter
E Norman
```
现在想要创建5个列表，每个列表分别包含*A* ~ *F*等级的学生姓名，可以使用如下方式：

```sh
$ look A evaluation | colrm 1 2 > a_level
$ look B evaluation | colrm 1 2 > b_level
$ look C evaluation | colrm 1 2 > c_level
$ look D evaluation | colrm 1 2 > d_level
$ look E evaluation | colrm 1 2 > e_level
```

管道线前是分别搜索出以字母*A* ~ *F*开头的行，管道线后是移除每行的前两个字符，即等级，再将其写入相应等级文件中。

> 注意
比较遗憾的是，**look**不能从标准输入中读取数据，他必须从一个或多个文件中获取输入。
这意味着，**look**并不是一个严格的过滤器。

当希望`look`忽略标点符号和其他特殊字符时，可以使用`-d`选项。
当希望忽略大小写时，可以使用`-f`(fold，同等)选项。

----------


## 查找单词

前面提到可以使用`look`搜索字典文件。当希望查找所有以特定模式开头的单词时就可以这么做。当以这种方式使用`look`时，程序语法如下：

```sh
$ look pattern
```
其中*pattern*是搜索的模式。

**字典文件**实际上并不是一个字典，他是一个长的、综合的单词列表，字典文件从Unix初期一直存在并一直更新。字典文件中的单词都是按字母顺序排序的，每行一个单词，因此可以很方便地使用`look`进行搜索。
当您想键入单词*simultaneous*时，但是不确定他的拼写，那么可以输入：

```sh
$ look simult
simultaneity
simultaneous
simultaneously
simultaneousness
simultaneousnesses
simulty
```
现在就可以从中选择正确的单词了。

----------


> 提示
当使用**vi**编辑器时，通过使用**:r !**可以发送一个快速的**lppk**命令，并显示一列单词。例如：
**:r !look simult**
该命令将把所有以*simult*开头的单词插入到编辑缓冲区中，接下来就可以选择希望的单词，删除其他单词。

----------


# Reference

[1] Haeley Hahn. Unix & Linux 大学教程[M]. 张杰良, 译. 北京:清华大学出版社, 2010.1:400-411.
