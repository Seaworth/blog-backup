---
title: C++ day07 多态和头文件
date: 2019-04-08 21:19:07
toc: true
tags:
	- C++
---
> 人生的意义是从事你真正喜欢的、适合你的并有益于他人的工作。——《财富的自由之路》<!-- more -->

# 1 多态
C++支持编译时多态(**静态多态**)和运行时多态(**动态多态**)，运算符重载和函数重载就是编译时多态，而派生类和虚函数实现运行时多态。

静态多态和动态多态的区别就是函数地址是早绑定(静态联编)还是晚绑定(动态联编)。如果函数的调用，在编译阶段就可以确定函数的调用地址，并产生代码，就是静态多态。而如果函数的调用地址不能在编译期间确定，而需要在运行时才能决定，这就属于晚绑定。

真正的开发中，要遵循**开闭原则**：
1. 对扩展开放，对修改关闭。
2. 可以利用多态实现——有利于后期的扩展，结构性好，可读性高，但是效率稍微低一点（相比于C语言），因为发生多态的内部结构要复杂一点.

**多态案例-计算器**
```
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Calculate
{
public:
	//虚函数
	//virtual int getResult(){ return 0; }
	//纯虚函数：如果父类中有了纯虚函数，子类继承父类，就必须实现纯虚函数
	//如果父类中有了纯虚函数，这个父类就不能实例化对象
	//类中有纯虚函数，这个类叫做抽象类
	virtual int getResult() = 0;


	void setVal(int in_val1, int in_val2)
	{
		this->val1 = in_val1;
		this->val2 = in_val2;
	}
	int getVal1()
	{
		return val1;
	}
	int getVal2()
	{
		return val2;
	}
private:
	int val1;
	int val2;
};

class AddCalculate:public Calculate
{
public:
	virtual int getResult()
	{
		return getVal1() + getVal2();
	}
};

//扩展减法
class SubCalculate :public Calculate
{
public:
	virtual int getResult()
	{
		return getVal1() - getVal2();
	}
};

void test01()
{
	Calculate *cal;
	cal = new AddCalculate;
	cal->setVal(10, 20);
	cout << cal->getResult() << endl;

	delete cal;
	cal = new SubCalculate;
	cal->setVal(10, 20);
	cal->getResult();
	cout << cal->getResult() << endl;
 
}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 2 编写头文件

为了确保各个文件中类的定义一致，类通常被定义在头文件中，而且类所在头文件的名字应和类的名字一样。

**预处理**

确保头文件多次包含依然能够安全工作的是预处理器。预处理器是在编译前执行一段程序，可以部分改变我们所写的程序。`#include`也是一项预处理功能，当预处理器看到`#include`标记时，就会用指定的头文件的内容代替`#include`。

另外一项预处理功能**头文件保护符**，**头文件保护符**依赖于预处理变量，预处理变量分为两种状态：**已定义**和**未定义**。

`#define`指令：把一个名字设定为预处理变量。

下面的两个指令分别检查某个指定的预处理变量是否已经定义。
`#ifdef`指令：当且仅当变量已定义时为真。
`#ifndef`指令：当且仅当变量未定义时为真。
一旦检查结果为真，则后续操作直到遇见`#endif`指令为止。

使用这些功能就能有效防止多次包含的发生。
`Hero.h`文件
```
#ifndef HERO_H
#define HERO_H

#include <iostream>
using namespace std;
#include <string>

class Hero
{
public:
	Hero();

	string name;//名字
	int atk;//攻击力
	int def;//防御
	int hp;//血量
};

#endif
```

第一次包含Hero.h时，`#ifndef`的结果为真，预处理器将顺序执行后面的操作，直到遇见`#endif`。此时，预处理变量HERO_H已经定义。后面如果再次包含Hero.h，则#ifndef的检查结果为假，编译器将忽略`#ifndef`与`#endif`之间的部分。

**整个程序的头文件保护符必须唯一，为了避免与程序中的其他实体发生名字冲突，一般把预处理变量的名字全部大写。**

