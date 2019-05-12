---
title: C++ day10 STL常用容器
date: 2019-05-11 11:17:46
toc: true
tags:
	- C++
---
> 时间和爱都是金钱买不到的，这两样都是伴随人很久的。很幸运能掌握自己的时间，也有足够的金钱做自己想做的事情。——巴菲特
做自己想做的事情是最重要的。——芒格<!-- more -->

STL(Standard Template Library，标准模板库)，是惠普实验室开发的一系列软件的统称。STL 从广义上分为: 容器(container)，算法(algorithm) 迭代器(iterator)，容器和算法之间通过迭代器进行无缝连接。STL具有**高可重用性**，**高性能**，**高移植性**，**跨平台**的优点。

# 1 STL三大组件
## 1.1 容器
STL容器就是将运用最广泛的一些数据结构实现出来。
常用的数据结构：
- 数组(array)
- 链表(list)
- 树(tree)
- 栈(stack)
- 队列(queue)
- 集合(set)
- 映射表(map)

根据数据在容器中的排列特性，这些数据分为**序列式容器**和**关联式容器**两种。	序列式容器强调值的排序，序列式容器中的每个元素均有固定的位置，除非用删除或插入的操作改变这个位置。vector容器、deque容器、list容器等。	关联式容器是非线性的树结构，更准确的说是二叉树结构。各元素之间没有严格的物理上的顺序关系，也就是说元素在容器中并没有保存元素置入容器时的逻辑顺序。关联式容器另一个显著特点是：在值中选择一个值作为关键字key，这个关键字对值起到索引的作用，方便查找。set/multiset容器 map/multimap容器。

## 1.2 算法
以有限的步骤，解决逻辑或数学上的问题，叫做算法(Algorithms)。STL收录的算法经过了数学上的效能分析与证明，是极具复用价值的，包括常用的排序，查找等等。特定的算法往往搭配特定的数据结构，算法与数据结构相辅相成。

## 1.3 迭代器
迭代器(iterator)是一种抽象的设计概念，现实程序语言中并没有直接对应于这个概念的实物。`iterator`模式定义如下：提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。

# 2 常用容器
## 2.1 string容器
### 2.1.1 string的基本概念
C风格字符串(以空字符`\0`结尾的字符数组)太过复杂难于掌握，不适合大程序的开发，所以C++标准库定义了一种string类，定义在头文件<string>。
string和c风格字符串对比：
- char*是一个指针，string是一个类
- string封装了很多实用的成员方法，查找find，拷贝copy，删除delete 替换replace，插入insert
- 不用考虑内存释放和越界

### 2.1.2 string的常用操作
[string API 官方文档][1]
```
//构造函数
string s1;
string s2(s1);
string s3("hello world!");
string s4 = "i love C++.";
string s5(3, '6');//666
```
```
//字符串拼接
void test03()
{
	string s1("hello ");
	string s2("baby");
	s1 += s2;
	cout << s1 << endl;

	s1.append(", good morning!");
	cout << s1 << endl;
}
```
```
//字符串的查找
void test04()
{
	string s1("abcdefghijk");
	int positon;
	positon = s1.find("bc");
	cout << "position = " << positon << endl;

	int position2;
	position2 = s1.rfind('bc');//逆序查找
	cout << "position2 = " << position2 << endl;

	//字符串的替换
	s1.replace(1, 3, "111222");
	cout << s1 << endl;
}
```

```
//string子串
void test06()
{
	string s1 = "hello world.";
	string s2 = s1.substr(1, 3);
	cout << s2 << endl;

	//需求：查找邮箱的用户名
	string email = "baichuanemail@qq.com";
	int position = email.find("@");
	cout << position << endl;
	string userName=email.substr(0,position);
	cout << "userName = " << userName << endl;
}
```

```
//练习：验证码中不区分大小写，内部进行了大小写的转换（都转换为大写或都转换为小写）
//函数内部将string字符串中的所有小写字母都变为大写字母。
void test10()
{
	string s1 = "aBcDeFgH";
	for (auto &c : s1)
	{
		c = toupper(c);
		cout << c;
	}
	cout << endl;
	for (auto c : s1)
	{
		c = tolower(c);
		cout << c;
	}
	cout << endl;
}
```

## 2.2 vector容器
### 2.2.1 vector容器基本概念
vector的数据安排以及操作方式，与array非常相似，两者的唯一差别在于空间的运用的灵活性。array是静态空间，一旦配置了就不能改变，要换大一点或者小一点的空间，一切琐碎得由自己来，首先配置一块新的空间，然后将旧空间的数据搬往新空间，再释放原来的空间。vector是动态空间，随着元素的加入，它的内部机制会自动扩充空间以容纳新元素。

<img src="1.png" width="50%" height="50%" alt="vector容器"/>

### 2.2.2 vector迭代器
vector维护一个线性空间，所以不论元素的型别如何，普通指针都可以作为vector的迭代器，vector支持随机存取。
### 2.2.3 vector数据结构
vector所采用的数据结构非常简单，线性连续空间，它以两个迭代器_Myfirst和_Mylast分别指向配置得来的连续空间中目前已被使用的范围，并以迭代器_Myend指向整块连续内存空间的尾端。
为了降低空间配置时的速度成本，vector实际配置的大小可能比客户端需求大一些，以备将来可能的扩充，这边是容量的概念。换句话说，**一个vector的容量永远大于或等于其大小，一旦容量等于大小，便是满载，下次再有新增元素，整个vector容器就得另觅居所。**
### 2.2.4 vector常用API
[string API 官方文档][2]

## 2.3 deque容器
### 2.3.1 deque容器基本概念
vector容器是单向开口的连续内存空间，deque则是一种双向开口的连续线性空间。所谓的双向开口，意思是可以在头尾两端分别做元素的插入和删除操作，当然，vector容器也可以在头尾两端插入元素，但是在其头部操作效率很差，无法被接受。
deque容器和vector容器最大的差异，一在于deque允许使用常数项时间对头端进行元素的插入和删除操作。二在于deque没有容量的概念，因为它是动态的以分段连续空间组合而成，随时可以增加一段新的空间并链接起来。deque容器的复杂度远高于vector，除非有必要，尽可能使用vector，而不是deque。对deque进行的排序操作，为了最高效率，可将deque先完整的复制到一个vector中，对vector容器进行排序，再复制回deque。

<img src="2.png" width="50%" height="50%" alt="deque容器"/>

deque是由一段一段的定量的连续空间构成。一旦有必要在deque前端或者尾端增加新的空间，便配置一段连续定量的空间，串接在deque的头端或者尾端。deque最大的工作就是维护这些分段连续的内存空间的整体性的假象，并提供随机存取的接口，避开了重新配置空间，复制，释放的轮回，代价就是复杂的迭代器架构。
既然deque是分段连续内存空间，那么就必须有中央控制，维持整体连续的假象，数据结构的设计及迭代器的前进后退操作颇为繁琐。deque代码的实现远比vector或list都多得多。

### 2.3.2 deque常用API
[deque API 官方文档][3]

## 2.4 stack容器
### 2.4.1 stack容器基本概念
stack是一种先进后出(First In Last Out，FILO)的数据结构，它只有一个出口，形式如图所示。stack容器允许新增元素，移除元素，取栈顶元素，但是除了最顶端外，没有任何其他方法可以存取stack的其他元素。换言之，stack不提供遍历功能，也不提供迭代器。
有元素推入栈的操作称为push，将元素推出stack的操作称为pop。

<img src="3.png" width="30%" height="30%" alt="stack容器"/>

### 2.4.2 stack容器常用API
[stack API 官方文档][4]

## 2.5 queue容器
### 2.5.1 queue容器基本概念
queue是一种先进先出(First In First Out，FIFO)的数据结构，它有两个出口，queue容器允许从一端新增元素，从另一端移除元素。queue不提供遍历功能，也不提供迭代器。

<img src="4.png" width="50%" height="50%" alt="queue容器"/>

### 2.5.2 queue容器常用API
[queue API 官方文档][5]

## 2.6 list容器 
### 2.6.1 list容器基本概念
链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的**指针**链接次序实现的。链表由一系列结点（链表中每一个元素称为结点）组成，结点可以在运行时动态生成。每个结点包括两个部分：一个是存储数据元素的数据域，另一个是存储下一个结点地址的指针域。
相较于vector的连续线性空间，list就显得负责许多，它的好处是每次插入或者删除一个元素，就是配置或者释放一个元素的空间。因此，list对于空间的运用有绝对的精准，一点也不浪费。而且，对于任何位置的元素插入或元素的移除，list永远是常数时间。
**list和vector是两个最常被使用的容器。**

<img src="5.png" width="50%" height="50%" alt="list容器"/>

- 采用动态存储分配，不会造成内存浪费和溢出
- 链表执行插入和删除操作十分方便，修改指针即可，不需要移动大量元素
- 链表灵活，但是空间和时间额外耗费较大

### 2.6.2 list容器常用API
[list API 官方文档][6]
 
## 2.7 set/mutliset容器
### 2.7.1 set/mutliset容器基本概念
set的特性：所有元素都会根据元素的键值自动被排序。set的元素不像map那样可以同时拥有实值和键值，set的元素即是键值又是实值。set不允许两个元素有相同的键值。可以通过set的迭代器改变set元素的值吗？**不行**，因为set元素值就是其键值，关系到set元素的排序规则。如果任意改变set元素值，会严重破坏set组织。换句话说，set的iterator是一种**const_iterator**。
multiset特性及用法和set完全相同，唯一的差别在于它允许键值重复。set和multiset的底层实现是红黑树，**红黑树**为平衡二叉树的一种。

### 2.7.2 set/mutliset容器常用API
[set API 官方文档][7]

### 2.7.3 pair对组
对组(pair)将一对值组合成一个值，这一对值可以具有不同的数据类型，两个值可以分别用pair的两个公有属性first和second访问。
```
//第一种方法创建一个对组
pair<string, int> pair1(string("name"), 20);
cout << pair1.first << endl; //访问pair第一个值
cout << pair1.second << endl;//访问pair第二个值
//第二种
pair<string, int> pair2 = make_pair("name", 30);
cout << pair2.first << endl;
cout << pair2.second << endl;
```
## 2.8 map/multimap容器
### 2.8.1 map/multimap容器基本概念
map的特性是，所有元素都会根据元素的键值key自动排序。map所有的元素都是pair，同时拥有键值key和实值value，pair的第一元素被视为键值key，第二元素为实值value，map不允许两个元素有相同的键值。
我们可以通过map的迭代器改变map的键值吗？不行，因为map的键值关系到map元素的排列规则，任意改变map键值将会严重破坏map组织。如果想要修改元素的实值，那么是可以的。
multimap和map的操作类似，唯一区别multimap键值可重复。
Map和multimap都是以**红黑树**为底层实现机制。

### 2.8.2 map/multimap容器常用API
[map API 官方文档][8]

# 3 STL容器使用时机
|      | vector | deque | list | set    | multiset | map  | multimap |
| :--: | :----: | :---: | :--: | :----: | :---:    | :--: | :----:   |
| 典型内存结构 | 单端数组 | 双端数组 | 双向链表 | 二叉树 | 二叉树    | 二叉树 | 二叉树   |
| 可随机存取 | 是 | 是 | 否 | 否 | 否    | 对key而言，不是 | 否   |
| 元素搜寻速度 | 慢 | 慢 | 非常慢 | 快 | 快    | 对key而言，快 | 对key而言，快   |
| 元素安插移除 | 尾端 | 头尾两端 | 任何位置 | - | -    | - | -   |


[1]:http://www.cplusplus.com/reference/string/string/
[2]:http://www.cplusplus.com/reference/vector/vector/?kw=vector
[3]:http://www.cplusplus.com/reference/deque/deque/?kw=deque
[4]:http://www.cplusplus.com/reference/stack/stack/?kw=stack
[5]:http://www.cplusplus.com/reference/queue/queue/?kw=queue
[6]:http://www.cplusplus.com/reference/list/list/?kw=list
[7]:http://www.cplusplus.com/reference/set/set/?kw=set
[8]:http://www.cplusplus.com/reference/map/map/?kw=map