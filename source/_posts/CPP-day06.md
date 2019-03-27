---
title: C++ day06 继承
date: 2019-03-25 20:54:43
toc: true
tags:
	- C++
---
# 1 继承概述
为什么需要继承？<!-- more -->

{% asset_img 1.png 继承 %}

因为有些时候，两个类中大部分的属性和方法相同，只有少量的属性和方法有区别。此时使用**继承**可以复用已有的代码，减少代码的冗余性。

C++最重要的特征是代码重用，通过继承机制可以利用已有的数据类型来定义新的数据类型，新的类不仅拥有旧类的成员，还拥有新定义的成员。

派生类定义格式：
```
Class 派生类名 :  继承方式 基类名
{
	//派生类新增的数据成员和成员函数
}

```
三种继承方式： 
- public：公有继承
- protected：保护继承
- private：私有继承

单继承：指每个派生类只直接继承了一个基类的特征。
多继承：指多个基类派生出一个派生类的继承关系，多继承的派生类直接继承了不止一个基类的特征。

派生类访问控制的权限：
派生类继承基类，派生类拥有基类中全部成员变量和成员方法（除了构造和析构之外的成员方法），但是在派生类中，继承的成员并不一定能直接访问，不同的继承方式会导致不同的访问权限。

{% asset_img 2.png 派生类访问控制的权限 %}


# 3 继承中的构造与析构
## 3.1 继承中的对象模型
在C++编译器的内部可以理解为结构体，子类是由父类成员叠加子类新成员而成。
`test.cpp`
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Father
{
public:
	int a;
protected:
	int b;
private:
	int c;
};

//子类中会继承父类的私有成员，只是被编译器给隐藏起来了，访问不到私有成员
class Son1 :public Father
{
public:
	int d;
};

void test01()
{
	cout << sizeof(Son1) << endl;
}

//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
运行结果是`16`。
上述代码的保存路径为`F:\C++\C++_learning\01 关系运算符重载\06 继承中的对象模型`。

通过`VS2013 开发人员命令提示`进行查看上述案例的对象模型。
在我的电脑上该工具的路径为`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\Tools\Shortcuts`，如下图所示。

{% asset_img VS2013tool.png VS2013开发人员命令提示 %}

打开该命令工具后，切换到该工程目录下，然后打印其对象模型。
```
F:\>cd F:\C++\C++_learning\01 （关系运算符重载\06 继承中的对象模型）
dir （打印该路径的目录，可以看到test.cpp文件）
cl /d1 reportSingleClassLayoutSon1 test.cpp （输入该命令，打印Son1的对象模型）
```

{% asset_img 3.png 对象模型 %}
	
可以从红色方框中看出，Son1类从Father基类继承的成员有`a`，`b`，`c`，添加了一个成员`d`。所以`sizeof(Son1)`的结果是`16`（4个int成员变量，每个int是4byte）。

## 3.2 继承中的构造和析构
在C++编译器的内部可以理解为结构体，子类是由父类成员叠加子类新成员而成。

- 子类对象在创建时会首先调用父类的构造函数
- 父类构造函数执行完毕后，才会调用子类的构造函数
- 当父类构造函数有参数时，需要在子类初始化列表(参数列表)中显示调用父类构造函数
- 析构函数调用顺序和构造函数相反

# 4 继承中的同名处理
- 当子类成员和父类成员同名时，子类依然继承父类的同名成员。
- 如果子类成员和父类成员同名时，子类访问其成员时，默认访问子类的成员（就近原则）。
- 如果要访问父类的同名成员时，需要通过作用域::进行同名成员区分。

# 5 继承中的静态成员
父类中的静态成员属性，子类可以继承下来。
父类中的静态成员函数，子类对象可以通过作用域::访问。
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Base
{
public:
	static int a;
	static void func()
	{
		cout << "base void func()" << endl;
	}
	static void func(int val)
	{
		cout << "base void func(int val)" << endl;
	}
};
int Base::a = 10;
class Son :public Base
{
public:
	static int a;
	static void func()
	{
		cout << "son void func()" << endl;
	}
};
int Son::a = 20;
void test01()
{
	//静态成员属性，子类可以继承下来
	cout << Son::a << endl;
	cout << Base::a << endl;

	//访问父类的同名函数
	Son::func();
	Son::Base::func(10);
}

//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
运行结果：
```
20
10
son void func()
base void func(int val)
请按任意键继续. . .
```

# 6 多继承
> 可以从一个类继承，也能同时从多个类继承，这就是多继承。但是由于多继承是非常受争议的，从多个类继承可能会导致函数、变量等同名导致较多的歧义。

{% asset_img 4.png 多继承 %}

带来的问题：
如果两个基类中有同名的函数或者变量，那么通过派生类对象去访问这个函数或变量时就不能明确到底调用从基类1继承的版本还是从基类2继承的版本？
解决方法：
显示指定调用那个基类的版本。

**菱形继承**

{% asset_img 5.png 菱形继承 %}

这种继承所带来的问题：
1. B1继承了A的数据和函数，B2同样继承了A的数据和函数，当C调用函数或者数据时，就会产生二义性。
2. C继承自A的函数和数据继承了两份，这份数据我们只需要一份就可以。

对于这种**菱形继承**所带来的两个问题，C++为我们提供了一种方式：**采用虚基类**。

{% asset_img 6.png 菱形继承 %}
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Animal
{
public:
	int age;
};

class Sheep:virtual public Animal
{

};
class Tuo :virtual public Animal
{

};

class SheepTuo :public Sheep, public Tuo
{

};
//解决菱形问题，用虚继承
void test01()
{
	SheepTuo s1;
	s1.Sheep::age = 10;
	s1.Tuo::age = 20;

	cout << s1.Sheep::age << endl;
	cout << s1.Tuo::age << endl;
	cout << s1.age << endl;//虚继承解决了二义性，只有一份age

	cout << "-----------以下为虚继承的工作原理-----------" << endl;

	SheepTuo s2;
	s2.age = 666;
	//Sheep的偏移量
	cout << *((int*)(*(int*)(&s2)) + 1) << endl;
	//Tuo的偏移量
	cout << *((int*)(*((int*)&s2+1)) + 1)<< endl;
	//输出age
	cout<<((Animal*)((char*)&s2 + 8))->age << endl;

}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
} 
```
运行结果：

{% asset_img 7.png 结果1 %}

从结果中看出虚继承只有一份age，输出3个20。
我们可以通过前面介绍的方法，在`VS2013 开发人员命令提示`上进行查看的SheepTuo对象模型，如下图：

{% asset_img 8.png 结果2 %}

可以看出虚继承，只存在一份**age**。
1.Animal菱形最顶层的类，内存布局图没有发生改变。
2.Sheep和Tuo通过虚继承的方式派生自Animal，这两个对象的布局图中可以看出编译器为我们的对象中增加了一个**vbptr** (virtual base pointer)，**vbptr**指向了一张表，这张表保存了当前的虚指针相对于虚基类的首地址的偏移量。
3.SheepTuo派生于Sheep和Tuo，继承了两个基类的vbptr指针，并调整了vbptr与虚基类的首地址的偏移量。

> 工程开发中真正意义上的多继承是几乎不被使用，因为多重继承带来的代码复杂性远多于其带来的便利，多重继承对代码维护性上的影响是灾难性的，在设计方法上，任何多继承都可以用单继承代替。





















