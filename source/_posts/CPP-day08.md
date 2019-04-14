---
title: C++ day08 模板
date: 2019-04-14 11:06:39
toc: true
tags:
	- C++
---
> 谁控制过去，谁就控制未来；谁控制现在，谁就控制过去。——《1984》<!-- more -->

# 1 函数模板
C++提供了函数模板(function template)，所谓函数模板，实际上是建立一个通用函数，其函数类型和形参类型不具体制定，用一个虚拟的类型来代表。这个通用函数就成为函数模板。凡是函数体相同的函数，但是参数的类型不同，都可以用这个模板代替，不必定义多个函数，只需在模板中定义一次即可。在调用函数时系统会根据实参的类型来取代模板中的虚拟类型，从而实现不同函数的功能。

## 1.1 函数模板基本用法
设计一个函数，功能是交换两个变量的值。其基本的用法如下：
```
//函数模板
template<class T> //同template<typename T>
void mySwap(T &a1, T &a2)
{
	T temp = a1;//声明一个临时值
	a1 = a2;
	a2 = temp;
}
```
调用以上的函数模板，有两种方式：
1. 自动类型推导
2. 显示类型调用

```
//测试函数
void test01()
{
	int a1 = 1;
	int a2 = 30;
	//1自动类型推导
	mySwap(a1, a2);
	cout << "a1= " << a1 << endl;
	cout << "a2= " << a2 << endl;

	//2显式类型调用
	mySwap<int>(a1, a2);
	cout << "a1= " << a1 << endl;
	cout << "a2= " << a2 << endl;
}
```

函数模板与普通函数的区别：函数模板不可以进行隐式类型转换，而普通函数可以进行隐式类型转化。

函数模板和普通函数的调用规则：
1. 如果函数模板和普通函数重载（同名），优先调用普通函数，如果要强制调用函数模板，可以使用空参数列表myPrint<>(a,b)。
2. 函数模板可以发生重载。
3. 如果函数模板可以产生更好的匹配时，优先调用函数模板。

## 1.2 模板实现的机制
HelloWorld.cpp是高级语言程序，是方便人阅读和编写，机器是不能够直接识别的，机器只能够识别机器指令（二进制文件）。为了在系统上能够运行HelloWorld.cpp程序，每一条语句都需要转化为机器指令。然后将这些机器指令打包成可执行目标文件格式，并以二进制形式存储在磁盘中。

预处理(Pre-processing)`->`编译(Compiling)`->`汇编(Assembling)`->`链接(Linking)

函数模板机制，并不是通过把函数模板处理成任何类型的函数，只是通过传递的类型参数生成不同的函数，编译器会对函数模板进行**两次编译**，在声明的地方对模板代码本身进行编译，在调用的地方对参数替换后的代码进行编译。

## 1.3 模板的局限性和解决方法
假设有如下函数模板，用来比较两个对象是否相等。
```
template<class T>
bool myCompare(T &a, T &b)
{
	if (a == b)
	{
		return true;
	}
	return false;
}
```
当调用函数模板的时候，传入的是自定义参数类型`class Person`，该函数则无法正确处理。为了解决这种问题，可以提供模板的重载，为这些特定的类型提供具体化的模板。
```
template<> bool myCompare<Person>(Person &a, Person &b)
{
	if (a.age == b.age)
	{
		return true;
	}
	return false;
}
```
# 2 类模板
# 2.1 类模板基本用法

类模板和函数模板的定义和使用相似。有时候，两个类的功能相同，仅仅数据类型不同，则可以使用类模板。用法如下：
```
template<class nameType,class ageType=int>//如果没有传递类型，则默认为int
class Person
{
public:
	Person(nameType in_name, ageType in_age)
	{
		this->name = in_name;
		this->age = in_age;
	}
	void show()
	{
		cout << "name: " << this->name << endl;
		cout << "age: " << this->age << endl;
	}
public:
	nameType name;
	ageType age;
};

//测试函数
void test01()
{
	Person<string, int> p1("孙悟空", 1000);
	Person<string> p2("猪八戒", 2000);//默认类型
	p1.show();
	p2.show();
}
```

类模板：
1. 可以有默认的类型
2. 类模板不可以类型推导，只能够显式调用

# 2.2 类模板做函数参数

三种使用方式：
1. 指定传入类型
2. 参数模板化
3. 整体模板化

> 小技巧：typeid().name() 打印变量的类型

```
//1. 指定传入类型
void doWork1(Person<string, int> &p)
{
	p.show();
}
void test01()
{
	Person<string, int> p("aa", 12);
	doWork1(p);
}
//2. 参数模板化
template<class T1,class T2>
void doWork2(Person<T1, T2> &p)
{
	cout << typeid(T1).name() << endl;
	cout << typeid(T2).name() << endl;
	p.show();
}
void test02()
{
	Person<string, int> p("bb", 13);
	doWork2(p);
}
//3. 整体模板化
template<class T>
void doWork3(T &p)
{
	cout << typeid(T).name() << endl;
	p.show();
}
void test03()
{
	Person<string, int> p("cc", 14);
	doWork3(p);
}
```

# 2.3 类模板的继承

有一个Base类模板，现在要从Base类模板派生出普通函数Son1和模板函数Son2。需要注意的是，Son1和Son2继承与Base，必须指明Base的T的类型，否则无法分配内存。

```
//基类模板
template<class T>
class Base
{
public:
	T a;
};

//派生出普通函数
class Son :public Base<int>
{

};
//派生出模板函数
template<class T1,class T2>
class Son1 :public Base<T2>
{
public:
	Son1()
	{
		cout << typeid(T1).name() << endl;
		cout << typeid(T2).name() << endl;
	}
public:
	T1 b;
};
```

# 2.4 类模板的头文件（分文件编写）

如果将类模板进行分文件编写，Person.h和Person.cpp分别写声明和实现，会出现问题，由于类模板的成员函数在运行的阶段才去创建，导致只包含Person.h头文件，不会去创建成员函数的实现，所以在链接的过程中，无法解析外部命令。

解决方案：
1. 包含Person.cpp文件，`#include "Person.cpp"`（不推荐）
2. 类模板不要进行分文件编写，写到一个文件中，类内进行声明，类外进行实现，最后将名字改为Person.hpp，这是约定俗成的。

**Person.hpp**
```
#ifndef PERSON_H
#define PERSON_H

#include <iostream>
#include <string>
using namespace std;

template<class nameType, class ageType = int>
class Person
{
public:
	Person(nameType in_name, ageType in_age);

	void show();
public:
	nameType name;
	ageType age;
};

template<class nameType, class ageType>
Person<nameType, ageType>::Person(nameType in_name, ageType in_age)
{
	this->name = in_name;
	this->age = in_age;
}

template<class nameType, class ageType>
void Person<nameType, ageType>::show()
{
	cout << "name: " << this->name << endl;
	cout << "age: " << this->age << endl;
}
#endif
```





