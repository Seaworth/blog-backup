---
title: C++ day04
date: 2019-03-04 20:12:37
toc: true
tags:
	- C++
---
> 真正的光明绝不是没有黑暗的时间，只是永远不被黑暗掩蔽罢了。真正的英雄绝不是没有卑下的情操，只是永不被卑下的情操所屈服罢了。——《颠覆者》<!-- more -->

# 1 静态成员
在类定义中，成员包括**成员变量**和**成员函数**，这些成员可以用关键字**`static`**声明为静态的，称为**静态成员**。
不管这个类创建了多少个对象，**静态成员只有一个拷贝**，这个拷贝被所有属于这个类的对象**共享**。
## 1.1 静态成员变量
类中的成员声明为**static**，这种成员称为**静态成员变量**。与一般的数据成员不同，无论建立多少个对象，都只有一个静态数据的拷贝。**静态成员变量属于类，所有对象共享**。
> 静态成员变量在编译的阶段就分配内存，对象还没有建立时，就分配了空间。
- 静态成员变量必须在**类中声明**，在**类外定义**。
- 静态数据成员不属于某个对象，在为对象分配空间中不包括静态成员所占空间。
- 静态数据成员可以通过类名或者对象名来引用。

## 1.2 静态成员函数
在类定义中，前面有static说明的成员函数称为**静态成员函数**。静态成员函数使用方式和静态变量一样，同样在对象没有创建前，即可通过类名调用。静态成员函数主要为了**访问静态变量**，但是，**不能访问普通成员变量**。
静态成员函数的意义，不在于信息共享，数据沟通，而在于管理静态数据成员，完成对静态数据成员的封装。
- 静态成员函数只能访问静态变量
- 静态成员函数也是有权限的private，public
- 普通成员函数既可以访问静态成员变量，也可以访问非静态成员变量

```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Person
{
public:
	//加入static就是静态成员变量，会共享数据，在类内声明，类外初始化
	static int age;

	//静态成员函数，不可以访问普通的成员变量，可以访问静态的成员函数
	static void func()
	{
		cout << "static void func()调用" << endl;
	}

	//普通的成员函数，可以访问普通成员变量，也可以访问静态成员变量
	void myFunc()
	{
		age = 100;
	}
private:
	static int other;
	static void func1()
	{
		cout << "static void func1()调用" << endl;
	}
};

//类外对静态成员进行初始化
int Person::age = 0;
int Person::other = 6;

//测试函数
void test01()
{
	//1 通过对象访问属性
	Person p1;
	p1.age = 1;
	
	Person p2;
	p2.age = 2;

	cout << "p1.age = " << p1.age << endl;
	cout << "p2.age = " << p2.age << endl;

	//2 通过类名访问属性
	cout << "通过类名访问属性age：" << Person::age << endl;

	//3 私有权限在类外无法访问
	//cout << "other = " << Person::other << endl;

	p1.func();
	p2.func();
	Person::func();
}

//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
# 2 单例模式
单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例的特殊类。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。
> Singleton（单例）：在单例类的内部实现只生成一个实例，同时它提供一个静态的getInstance()工厂方法，让客户可以访问它的唯一实例；为了防止在外部对其实例化，将其默认构造函数和拷贝构造函数设计为私有；在单例类内部定义了一个Singleton类型的静态对象，作为外部共享的唯一实例。

> 用单例模式，模拟公司员工使用打印机场景，打印机可以打印员工要输出的内容，并且可以累积打印机使用次数。
1. 默认构造函数私有化
2. 拷贝构造函数私有化
3. 内部维护一个对象指针
4. 私有化唯一指针
5. 对外提供getInstance方法来访问这个指针

```
class Printer
{
public:
	//静态函数
	static Printer* getInstance()
	{
		return singlePrinter;
	}
	void printText(string text)
	{
		cout << text << endl;
		count++;
		cout << "目前打印次数：" << count << endl;
	}
private:
	Printer(){ count = 0; }
	Printer(const Printer& p1){}
	static Printer* singlePrinter;

	//记录打印次数
	int count;
};

Printer* Printer::singlePrinter=new Printer;

//测试
void test01()
{
	//拿到打印机
	Printer* Printer = Printer::getInstance();
	Printer->printText("hello 单例模式");
	Printer->printText("hello 单例模式");
}
```
# 3 成员变量和成员函数的存储
- C++中的**非静态数据成员**直接内含在类对象中，就像c struct一样。
- 静态成员变量不属于对象
- 静态成员函数和非静态成员函数都不属于对象

结论：C++类对象中的变量和函数是分开存储，只有非静态成员变量属于对象。

```
class Person
{
public:
	int age;//非静态成员变量，属于对象
	void func(){}//非静态成员函数，不属于对象
	static int id;//静态成员变量，不属于对象
	static void func2(){}//静态成员函数，不属于对象
	//结论：只有非静态成员变量，属于对象
	double a;//4+8=12,字节对齐，16
};

void test01()
{
	//空类的大小为1，因为每个实例的对象都有一个独一无二的地址，char维护这个地址
	cout << "sizeof(Person): " <<sizeof(Person)<< endl;
}
```

# 4 this指针
## 4.1 this指针的工作原理
C++的数据和操作也是分开存储，也就是说多个同类型的对象会共用一块代码，这一块代码是如何区分哪个对象调用自己的呢？
{% asset_img 1.png 工作原理 %} 

C++通过提供特殊的对象指针，**this指针**，解决上述问题。this指针指向被调用的成员函数所属的对象。
## 4.2 this指针的使用
- 当形参和成员变量同名时，可用this指针来区分
- 在类的非静态成员函数中返回对象本身，可使用`return *this`

```
class Person
{
public:
	//1. 当形参名和成员变量名一样时，this指针可用来区分
	Person(int age)
	{
		this->age = age;
	}

	//2. 返回对象本身的引用
	Person& addAge(Person &p1)
	{
		this->age += p1.age;
		return *this;//*this指向对象本体
	}
	int age;
};
```

## 4.3 const修饰成员函数
- 用const修饰的成员函数时，const修饰this指针指向的内存区域，成员函数体内**不可以**修改本类中的任何**普通成员变量**
- 当成员变量类型符前用**`mutable`**修饰时例外

```
class Person
{
public:
	Person()
	{
		this->m_a = 0;
		this->m_b = 0;
	}

	void showInfo() const//常函数，不允许修改指针指向的值
	{
		//this->m_a = 11;
		this->m_b = 11;
		cout << "m_a = " << m_a << endl;
		cout << "m_b = " << m_b << endl;
	}

	void show() const
	{
		//m_a = 22;
	}
//private:
	int m_a;
	mutable int m_b;//就算是常函数，也要执意修改
};
```

## 4.4 const修饰成员对象
- 常对象只能调用**const的成员函数**
- 常对象可访问const或非const数据成员，不能修改，除非成员用mutable修饰

# 5 友元
类的主要特点之一是**数据隐藏**，即类的私有成员无法在类的外部(作用域之外)访问。但是，有时候需要在类的外部访问类的私有成员，怎么办？
> 解决方法:
使用友元函数，友元函数是一种特权函数，C++允许这个特权函数访问私有成员。
举个例子：家里有客厅、卧室，客厅是Public的，所有的客人都可以进去，但是你的卧室是private的，只有你能进去，但是，你也可以允许你的好朋友进去。
程序员可以把一个全局函数、某个类中的成员函数、甚至整个类声明为友元。
1. 全局函数作友元
2. 类作友元
3. 类的成员函数作友元

```
class Building;
//友元类
class MyFriend{
public:
	//友元成员函数
	void LookAtBedRoom(Building& building);
	void PlayInBedRoom(Building& building);
};
class Building{
	//全局函数做友元函数
	friend void CleanBedRoom(Building& building);
#if 0
	//成员函数做友元函数
	friend void MyFriend::LookAtBedRoom(Building& building);
	friend void MyFriend::PlayInBedRoom(Building& building);
#else	
	//友元类
	friend class MyFriend;
#endif
public:
	Building();
public:
	string mSittingRoom;
private:
	string mBedroom;
};

void MyFriend::LookAtBedRoom(Building& building){
	cout << "我的朋友参观" << building.mBedroom << endl;
}
void MyFriend::PlayInBedRoom(Building& building){
	cout << "我的朋友玩耍在" << building.mBedroom << endl;
}

//友元全局函数
void CleanBedRoom(Building& building){
	cout << "友元全局函数访问" << building.mBedroom << endl;
}

Building::Building(){
	this->mSittingRoom = "客厅";
	this->mBedroom = "卧室";
}

int main(){

	Building building;
	MyFriend myfriend;

	CleanBedRoom(building);
	myfriend.LookAtBedRoom(building);
	myfriend.PlayInBedRoom(building);

	system("pause");
	return EXIT_SUCCESS;
}
```

**特别注意**
在用VS2010，新建的项目名称为`10 成员函数做友元函数`，这个例子中，编译器会提示如下错误

{% asset_img 2.png 警告 %}

但点击`生成`->重新生成 `10 成员函数做友元函数`，又成功，没有失败，但错误列表中仍提示错误。

{% asset_img 3.png 输出 %}

此时估计是编译器犯糊涂了，语法和程序都没有问题。


