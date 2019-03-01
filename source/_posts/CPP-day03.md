---
title: C++ day03
date: 2019-02-27 22:13:17
toc: true
tags:
	- C++
---
# 1 面向对象设计案例—点和圆的关系
- 需求分析：

设计一个圆形类Circle，和一个点类Point，计算点和圆的关系。<!-- more -->
假如圆心坐标为x0, y0, 半径为r，点的坐标为x1, y1：
1. 点在圆上：(x1 - x0)\*(x1 - x0) + (y1 - y0)\*(y1 - y0) == r\*r
2. 点在圆内：(x1 - x0)\*(x1 - x0) + (y1 - y0)\*(y1 - y0) < r\*r
3. 点在圆外：(x1 - x0)\*(x1 - x0) + (y1 - y0)\*(y1 - y0) > r\*r
- 采用**分文件编写**的方式
- 编程环境：VS2010

# 2 代码实现
## 2.1 新建一个空项目
1. 添加`02 面向对象设计案例—点和圆的关系.cpp`
2. 添加`circle.cpp`
3. 添加`point.cpp`
4. 添加头文件`circle.h`
5. 添加头文件`point.h`

建立好项目后如图所示：

{% asset_img 1.png 项目 %} 

## 2.2 相关代码
头文件`point.h`
```
#pragma once//避免头文件多次编译
#include <iostream>
using namespace std;

//点类
class Point
{
public:
	//设置x,y
	void setX(int in_x);
	void setY(int in_y);
	//得到x,y
	int getX() const;
	int getY() const;

private:
	int x;
	int y;
};
```

`point.cpp`

```
#include "point.h"

//设置x,y
void Point::setX(int in_x)
{
	x = in_x;
}
void Point::setY(int in_y)
{
	y = in_y;
}
//得到x,y
int Point::getX() const
{
	return x;
}
int Point::getY() const
{
	return y;
}
```

头文件`circle.h`
```
#pragma once//避免头文件被多次编译
#include <iostream>
using namespace std;
#include "point.h"

//圆类
class Circle
{
public:
	//设置圆心和半径
	void setCenter(Point in_center);
	void setR(int in_r);

	//获得圆心和半径
	Point getCenter() const;
	int getR() const;

	//比较点和圆的关系
	void isInCircle(Point p1) const;

private:
	//圆心
	Point center;
	//半径
	int r;
};
```

`circle.cpp`
```
#include "circle.h"

//设置圆心和半径
void Circle::setCenter(Point in_center)
{
	center = in_center;
}
void Circle::setR(int in_r)
{
	r = in_r;
}

//获得圆心和半径
Point Circle::getCenter() const
{
	return center;
}
int Circle::getR() const
{
	return r;
}

//比较点和圆的关系
void Circle::isInCircle(Point p1) const
{
	//点到圆心的距离的平方
	int distance = (p1.getX() - center.getX())*(p1.getX() - center.getX()) + (p1.getY() - center.getY())*(p1.getY() - center.getY());

	//半径的平方
	int squareOfR = r*r;

	//判断
	if (distance == squareOfR)
	{
		cout << "点在圆上。" << endl;
	}
	else if (distance > squareOfR)
	{
		cout << "点在圆外。" << endl;
	}
	else
	{
		cout << "点在圆内。" << endl;
	}
}
```

`02 面向对象设计案例—点和圆的关系.cpp`
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std
#include "circle.h"

//需求分析：
//设计一个圆形类（AdvCircle），和一个点类（Point），计算点和圆的关系。
//假如圆心坐标为x0, y0, 半径为r，点的坐标为x1, y1：
//1）点在圆上：(x1 - x0)*(x1 - x0) + (y1 - y0)*(y1 - y0) == r*r
//2）点在圆内：(x1 - x0)*(x1 - x0) + (y1 - y0)*(y1 - y0) < r*r
//3）点在圆外：(x1 - x0)*(x1 - x0) + (y1 - y0)*(y1 - y0) > r*r
//采用分文件编写的方式！！！

//测试函数
void test01()
{
	//新建一个点
	Point p1;
	p1.setX(10);
	p1.setY(10);

	//新建一个圆心
	Point center;
	center.setX(10);
	center.setY(0);
	//新建一个圆
	Circle c1;
	c1.setCenter(center);
	c1.setR(10);

	//判断
	c1.isInCircle(p1);
}

//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 3 构造函数和析构函数
构造函数主要作用在于创建对象时为对象的成员属性赋值，构造函数由编译器自动调用，无须手动调用。
析构函数主要用于**对象销毁前**系统自动调用，执行一些清理工作。
**构造函数分类**
按照参数分类：
- 无参构造函数
- 有参构造函数

按照类型分类：
- 普通构造函数
- **拷贝构造函数**

拷贝构造函数的调用时机：
- 用一个对象初始化另一个对象
- 对象以值传递的方式传给函数参数
- 函数局部对象以值传递的方式从函数返回(`vs debug`模式下调用一次拷贝构造，`qt`不调用任何构造)

# 4 浅拷贝和深拷贝
## 4.1 浅拷贝
同一类型的对象之间可以赋值，使得两个对象的成员变量的值相同，两个对象仍然是独立的两个对象，这种情况被称为**浅拷贝**.
一般情况下，浅拷贝没有任何副作用，但是**当类中有指针**，并且指针指向动态分配的内存空间，析构函数做了动态内存释放的处理，产生指针悬挂，会导致内存问题。
浅拷贝示意图：
{% asset_img 2.png 浅拷贝 %}

## 4.2 深拷贝
当类中有指针，并且此指针有动态分配空间，析构函数做了释放处理，往往需要自定义拷贝构造函数，自行给指针动态分配空间，**深拷贝**。
深拷贝示意图：
{% asset_img 3.png 深拷贝 %}

# 5 多个对象的构造和析构
## 5.1 初始化列表
> 注意：初始化成员列表（参数列表）只能在**构造函数**使用。

```
class Person{
public:
#if 0
	//传统方式初始化
	Person(int a,int b,int c){
		mA = a;
		mB = b;
		mC = c;
	}
#endif
	//初始化列表方式初始化
	Person(int a, int b, int c):mA(a),mB(b),mC(c){}
	void PrintPerson(){
		cout << "mA:" << mA << endl;
		cout << "mB:" << mB << endl;
		cout << "mC:" << mC << endl;
	}
private:
	int mA;
	int mB;
	int mC;
};
```
## 5.2 类对象作为成员
在类中定义的数据成员一般都是基本的数据类型。但是类中的成员也可以是对象，叫做**对象成员**。
C++中对象的初始化是非常重要的操作，当创建一个对象的时候，c++编译器必须确保调用了**所有子对象的构造函数**。如果所有的子对象有默认构造函数，编译器可以自动调用他们。但是如果子对象没有默认的构造函数，或者想指定调用某个构造函数怎么办？
那么是否可以在类的构造函数直接调用子类的属性完成初始化呢？但是如果子类的成员属性是私有的，我们是没有办法访问并完成初始化的。
> 解决办法非常简单：对于子类调用构造函数，c++为此提供了专门的语法，即**构造函数初始化列表**。
当调用构造函数时，首先按各对象成员在类定义中的顺序（和参数列表的顺序无关）依次调用它们的构造函数，对这些对象初始化，最后再调用本身的函数体。也就是说，先调用对象成员的构造函数，再调用本身的构造函数。
析构函数和构造函数调用顺序相反，先构造，后析构。

# 6 explicit关键字
C++提供了关键字explicit，禁止通过构造函数进行的隐式转换。声明为explicit的构造函数不能在隐式转换中使用。
> explicit注意
- 修饰构造函数，防止隐式转化。
- 针对**单参数**的构造函数(或者除了第一个参数外其余参数都有默认值的多参构造)而言。

```
public:
	explicit MyString(int n){
		cout << "MyString(int n)!" << endl;
	}
	MyString(const char* str){
		cout << "MyString(const char* str)" << endl;
	}
};

int main(){

	//给字符串赋值？还是初始化？
	//MyString str1 = 1;//加了explicit，会报错Erro：不存在从int转换到MyString的适当构造函数
	MyString str2(10);

	//寓意非常明确，给字符串赋值
	MyString str3 = "abcd";
	MyString str4("abcd");

	return EXIT_SUCCESS;
}
```

# 7 动态对象的创建
> 当我们创建数组的时候，总是需要提前预定数组的长度，然后编译器分配预定长度的数组空间，在使用数组的时，会有这样的问题，数组也许空间太大了，浪费空间，也许空间不足，所以对于数组来讲，如果能根据需要来分配空间大小再好不过。
所以动态意味着不确定性。

当创建一个C++对象时会发生两件事:
1. 为对象分配内存
2. 调用构造函数来初始化那块内存

## 7.1 new运算符
C++中解决**动态内存分配**的方案是把创建一个对象所需要的操作都结合在一个称为`new`的运算符里。当用`new`创建一个对象时，它就在**`堆`**里为对象分配内存并调用**构造函数**完成初始化。
```
Person* person = new Person;
```
相当于:
```
Person* person = (Person*)malloc(sizeof(Person));
	if(person == NULL){
		return 0;
	}
person->Init();
```
在堆里创建对象的过程变得简单了，只需要一个简单的表达式，它带有内置的长度计算、类型转换和安全检查。
## 7.2 delete运算符
new表达式的反面是delete表达式。delete表达式先调用析构函数，然后释放内存。正如new表达式返回一个指向对象的指针一样，delete需要一个对象的地址。
## 7.3 new和delete采用相同形式
使用`new`和`delete`在**堆**上创建数组非常容易。
**当创建一个对象数组的时候，必须对数组中的每一个对象调用构造函数。**
`new`配合`delete`，`new []`配合`delete []`
```
void test01()
{
	//栈区开辟
	//Person p1;

	//在堆区开辟
	Person *p1 = new Person;
	delete p1;
	Person *p2 = new Person[];
	delete [] p1;
	//所有new出来的对象都会返回该类型的指针
	//malloc返回的是void* 需要进行强制类型转换
	//malloc不会调用构造函数，new会调用构造函数
	//new是一个运算符，malloc是一个函数
	//释放堆区的空间，delete
	//delete也是一个运算符
	//molloc配合free，new配合delete
}	
```
