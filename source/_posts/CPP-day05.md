---
title: C++ day05 运算符重载
date: 2019-03-08 21:10:46
toc: true
tags:
	- C++
---
> 人生最后的高度可能都是一样的，就是一张病床的高度。人生是如此充满了不确定性，而过去的爱恨情仇与人的生命相比，是那么不值得一提。——《颠覆者》<!-- more -->

> 运算符重载，就是对已有的**运算符**重新进行定义，赋予其另一种功能，以适应不同的数据类型。在C++中，可以定义一个处理类的新运算符。这种定义很像一个普通的函数定义，只是函数的名字由关键字operator及其紧跟的运算符组成。它像任何其他函数一样，当编译器遇到适当的模式时，就会调用这个函数。


**注：不要滥用运算符重载，只有在能够使类的代码更容易写，容易读，才有理由重载运算符。对于内置的数据类型的所有运算符是不可能改变的。**
# 1 加号运算符重载
两种实现方法：
1. 类的成员函数实现+重载
2. 全局函数实现+重载

> 需求：
1. 定义一个Person类，含有两个成员变量a和b（public）
2. 分别用成员函数和全局函数实现+运算符重载

```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Person
{
public:
	Person(){}
	Person(int in_a, int in_b) :a(in_a), b(in_b){}
	~Person(){}

	//1：打开成员函数重载加号；0：关闭
	#if 0
		Person operator+ (const Person p1)
			{
				Person temp;
				temp.a = this->a + p1.a;
				temp.b = this->b + p1.b;
				return temp;
			}
	#endif
	
	int a;
	int b;
};

//1：打开全局函数，实现加法重载；0：关闭
#if 1
Person operator+ (const Person p1, const Person p2)
{
	Person temp;
	temp.a = p2.a + p1.a;
	temp.b = p2.b + p1.b;
	return temp;
}
#endif
void test01()
{
	Person p1(1,1);
	Person p2(2,2);
	Person p3=p1+p2;
	cout << "p3的a:" << p3.a << endl;
	cout << "p3的b:" << p3.b << endl;
}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 2 左移运算符重载
> 需求：
1. 定义一个Person类，含有两个成员变量a和b（public）
2. 用全局函数对<<运算符进行重载
3. 能够实现链式输出，如`cout<<p1<<p2<<endl;`

**不能用类的成员函数对<<进行重载，因为如果是Person的成员函数，那么调用就是Person << 不满足要求**
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std
//<<运算符重载
/*
1. 用全局函数进行<<重载
2. 参数是ostream& cout 和 Person p1
3. Person类中对重载函数进行友元函数声明，使其能够访问私有的成员变量
4. 要进行链式输出，需要返回哦stream&，从而保证返回值是cout
*/
class Person
{
	friend ostream& operator << (ostream& cout, Person p1);
public:
	Person(){}
	Person(int in_a, int in_b) :a(in_a), b(in_b){}
	~Person(){}

	//不能用类的成员函数对<<进行重载，因为如果是Person的成员函数，那么调用就是Person << 不满足要求

private:
	int a;
	int b;
};

ostream& operator << (ostream& cout, Person p1)
{
	cout << "a = " << p1.a << " b = " << p1.b << " ";
	return cout;
}

void test01()
{
	Person p1(1, 1);
	Person p2(2, 2);
	cout << p1 << p2 << endl;
}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 3 递增递减运算符重载
**前置++**：如++a，先++，再返回
**后置++**：如a++，先返回，后++
> 重载++运算符时，需要重载两个函数（前置++和后置++）。重载--运算符同理可得。
调用代码时候，要优先使用前缀形式，除非确实需要后缀形式返回的原值，前缀和后缀形式语义上是等价的，输入工作量也相当，只是效率经常会略高一些，由于前缀形式少创建了一个临时对象。
需求：
1. 定义一个Person类，含有一个成员变量age（private）
2. 重载++运算符，能够进行age加一
3. 重载--运算符，能够进行age减一
3. 重载<<运算符，能够打印年龄 

```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Person
{
	friend ostream& operator<< (ostream& cout, Person p1);
public:
	Person(){}
	Person(int in_age) :age(in_age){}
	~Person(){}

	//重载前置++，先++，再返回
	Person& operator++ ()
	{
		++(this->age);//前置++，效率稍高
		return *this;
	}
	//重载后置++，多一个占位参数进行区分
	//先返回，再++
	//后置++调用的时候，会多拷贝一次数据，比前置++的效率要差一些。
	Person operator++ (int)
	{
		Person temp = *this;//先用一个临时的值保存住要返回的值
		++(this->age);
		return temp;
	}

	//重载前置--
	Person& operator-- ()
	{
		--(this->age);
		return *this;
	}
	//重置后置--
	Person operator-- (int)
	{
		Person temp = *this;
		--(this->age);
		return temp;//返回值只能用值传递，以为返回的一个临时的变量，如果返回引用，临时变量会被释放，从而引发错误
	}
private:
	int age;
};

//重载<<
ostream& operator<< (ostream& cout, Person p1)
{
	cout << p1.age;
	return cout;
}
void test01()
{
	Person p1(0);
	cout << ++p1 << endl;//1
	cout << p1++ << endl;//1
	cout << p1 << endl;//2

	Person p2(10);
	cout << --p2 << endl;//9
	cout << p2-- << endl;//9
	cout << p2 << endl;//8

}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
# 4 指针运算符重载
> 实现一个智能指针（smartPointer）的类，用来管理Person类。如果在堆区new了一个Person类型的内存，忘记了释放就会导致内存泄漏，而smartPointer就是用来释放内存。
实现步骤：
1. 定义一个Person类，含有一个成员变量age（private）
2. 定义一个smartPointer类，含有一个成员变量Person* p，在析构函数中对p进行释放
3. 对smartPointer类，重载->和*运算符

```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

class Person
{
public:
	Person(){}
	Person(int in_age) :age(in_age){}
	~Person()
	{
		cout << "调用Person的析构函数" << endl;
	}

	void showAge()
	{
		cout << "年龄： " << age << endl;
	}

private:
	int age;
};
//智能指针
class smartPointer
{
public:
	smartPointer(Person * p1) :p(p1){}
	~smartPointer()
	{
		cout << "调用smartPointer的指针" << endl;
		if (this->p != NULL)//检查p中是否开辟了内存，如果不为空，则释放
		{
			delete this->p;
			this->p = NULL;
		}
	}

	//重载->
	Person* operator-> ()
	{
		return this->p;
	}

	//重载*
	Person& operator* ()//注意返回的是引用
	{
		return *(this->p);
	}
private:
	Person * p;
};

void test01()
{
	//Person* p1 = new Person(10);
	//如果忘记释放，那么就会造成内存泄漏

	smartPointer sp(new Person(10));//sp开辟到了栈上，自动释放
	sp->showAge();//sp->->showAge()，编译器做了优化
	(*sp).showAge();
}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 5 赋值运算符重载
**常见错误，当准备给两个相同对象赋值时，应该首先检查一下这个对象是否对自身赋值了。**
**`为什么要检查自我赋值？`**

1.**为了效率。自己给自己赋值完全没有意义，浪费开销，应该避免。如果是自我赋值，应该直接返回`* this`**。

2.**为了正确性。因为指针间的赋值（假设p2=p1），在复制前，需要将p2所指向的空间杀掉delete，然后重新为p2分配空间，再将`p1所指的内容`拷贝到`p2所指的空间`，如果是自我赋值，p1和p2是同一指针，在赋值操作前，杀掉p2所指的空间（即杀掉p1所指的空间），要赋值的数据（p1指向的内容）已被杀掉，程序报错。**

> 一个类默认创建四个函数：

1. 默认构造函数
2. 析构函数
3. 拷贝构造函数
4. operator=重载函数（只进行简单的值传递）

**当类中含有指针时，必须对=进行重载，从而避免浅拷贝**
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

//一个类默认创建四个函数：默认构造函数，析构函数，拷贝构造函数，operator=重载函数（只进行简单的值传递）
class Person
{
public:
	Person(int in_age,int in_id) :age(in_age),id(in_id){}
	~Person(){}
	int age;
	int id;
};

//1. =号容易混淆的地方
void test01()
{
	Person p1(10,1);
	Person p2(20, 2);
	Person p3 = p1;//调用拷贝构造，
	//如果一个对象还没有被创建，则必须初始化，即调用构造函数。
	//p3还没有初始化，所以会调用构造函数
	//p3是从p1创建而来，所以需要调用拷贝构造函数

	cout <<"p3: age = "<< p3.age << endl;
	cout <<"p3: id  = "<< p3.id  << endl;

	p2 = p1;//调用operator=函数
	//由于p2已经创建了，不用再调用构造函数了，只需调用重载的=运算符函数
	cout << "p2: age = " << p2.age << endl;
	cout << "p2: id  = " << p2.id  << endl;
}

//2. 类中含有指针
class Person2
{
public:
	Person2(const char* in_name)
	{
		this->name = new char[strlen(in_name) + 1];
		strcpy(this->name, in_name);
	}
	Person2& operator= (const Person2& p)//传入的是引用，函数返回值也是引用，能实现连续=
	{
		//检查是否是自我赋值
		if (this == &p)
		{
			return *this;
		}
		//如果判断原来已经堆区有内容，先释放
		if (this->name != NULL)
		{
			delete[] this->name;
			this->name = NULL;
		}
		this->name = new char[strlen(p.name) + 1];
		strcpy(this->name,p.name);
		return *this;
	}

	~Person2()
	{
		//类的成员中有指针，需要释放
		if (this->name != NULL)
		{
			delete[] this->name;
			this->name = NULL;
		}
	}
	char* name;
};


void test02()
{
	Person2 p1("老王");
	Person2 p2("大王");
	Person2 p3("小王");
	p2 = p1;//编译器默认的=重载是简单的值复制，如果类中含有指针，就会产生浅拷贝问题，释放指针的时候，就会造成两次释放，所以必须重载=
	cout << p2.name << endl;
	p3 = p2 = p1;//检查重载=
	p3 = p3;//检查自我赋值
	cout << p3.name << endl;
}
//函数入口
int main()
{
	test01();
	test02();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```



