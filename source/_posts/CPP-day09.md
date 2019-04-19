---
title: C++ day09 异常处理、标准IO流、文件读写
date: 2019-04-18 20:34:07
toc: true
tags:
	- C++
---
> 我希望每个程序员都能够明白工作、技术、写代码这些并不是你存在的意义，而生活才是，你需要懂得生活，学会生活。
- 生活：尝试有节制和有规律地生活，程序员生涯绝不是一次冲刺跑，而是一场马拉松。
- 睡眠：请记住保证充足的睡眠将使你变得更加强大。
- 旅行：这能够让你发现和感受到新的东西和事物，而这些无法从电脑屏幕上获取。
——《程序员的自我修养》<!-- more -->

# 0 类型转换
> 向上类型转换和向下类型转化：如下图所示，父类在上，子类在下。父类对象向子类转换，称向下类型转换，不安全。子类对象向父类转换，称向上类型转换，安全。安全与不安全的界定：指针是否指向了未知的区域，如果有则不安全。

**发生多态时，向上类型转换和向下类型转换总是安全的。因为一开始就给基类指针分配了派生类大小的内存空间，向上类型转换后，不会超出分配的内存空间，所以是安全的。**
{% asset_img 1.png 向上向下类型转换 %}

命名的强制类型转换
转换的形式如下：
*cast-name<type>(expression);*
其中，*type*是装换的目标类型，*expression*是要转换的值。*cast-name*是**static_cast**，**dynamic_cast**，**const_cast**，**reinterpret_cast**中的一种。*cast-name*指定了执行的是哪种转换。

**静态转换static_cast**
任何具有明确定义的类型转换，只要不包含底层的const，都可以使用*static_cast*。当需要把一个较大的算数类型赋值给较小的类型时，static_cast非常有用。此时，强制类型转换告诉程序的读者和编译器：我们知道并且不在乎潜在的精度损失。

**动态类型转换dynamic_cast**
基础类型都不可以转换，因为这会导致失去精度和不安全，都不让转换，非常严格。在进行下行转换的时候，**dynamic_cast**具有类型检查的功能，比static_cast更安全。

**dynamic_cast** 如果发生了多态，可以让基类转化为子类，向下转换，因为发生多态时，向上类型转换和向下类型转换总是安全的。

**常量转换const_cast**
不能直接对非指针和非引用进行常量转换。const_cast只能改变运算对象底层的const。

**建议：避免强制类型转换，因为干扰了正常的类型检查。**


# 1 异常处理
> 异常是指在运行时的反常行为，这些行为超出了函数的正常功能。

异常处理包括：
- throw表达式，异常检测使用throw表达式来表示它所遇到无法处理的问题。throw抛出异常。
- try语句块，用来处理异常。try语句块以try开始，一个或多个catch子句结束。

异常的捕捉方式是通过严格类型匹配。
```
void TestFunction(){
	
	cout << "开始抛出异常..." << endl;
	//throw 10; //抛出int类型异常
	//throw 'a'; //抛出char类型异常
	//throw "abcd"; //抛出char*类型异常
	string ex = "string exception!";
	throw ex;
}

int main(){

	try{
		TestFunction();
	}
	catch (int){
		cout << "抛出Int类型异常!" << endl;
	}
	catch (char){
		cout << "抛出Char类型异常!" << endl;
	}
	catch (char*){
		cout << "抛出Char*类型异常!" << endl;
	}
	catch (string){
		cout << "抛出string类型异常!" << endl;
	}
	//捕获所有异常
	catch (...){
		cout << "抛出其他类型异常!" << endl;
	}
	
	system("pause");
	return EXIT_SUCCESS;
}

```
异常接口声明：
在函数声明中列出可能抛出异常的所有类型，例如：void func() throw(A,B,C);这个函数func能够且只能抛出类型A,B,C
一个不抛任何类型异常的函数可声明为:void func() throw()
在VS环境下测试不成功，只有在`QT`和`Linux`下测试成功。

使用系统的标准异常，需要包含头文件`#include <stdexcept>`

**05 使用系统的标准异常.cpp**
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std
#include <string>
#include <stdexcept>

class Person
{
public:
	Person(string in_name, int in_age)
	{
		this->name = in_name;
		if (in_age<0 || in_age>200)
		{
			//throw out_of_range("范围越界");
			throw length_error("长度越界");
		}
		this->age = in_age;
	}

	string name;
	int age;
};

void test01()
{
	try
	{
		Person p1("seaworth", 201);
	}
	catch (out_of_range error)
	{
		cout << error.what() << endl;
	}
	catch (length_error error)
	{
		cout << error.what() << endl;
	}
}
//函数入口
int main()
{
	test01();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

# 2 标准输入输出流
**程序的输入**指的是从输入文件将数据传送给程序，**程序的输出**指的是从程序将数据传送给输出文件。
对系统指定的标准设备的输入和输出。即从键盘输入数据，输出到显示器屏幕。这种输入输出称为标准的输入输出，简称**标准I/O**。
以外存磁盘文件为对象进行输入和输出，即从磁盘文件输入数据，数据输出到磁盘文件。以外存文件为对象的输入输出称为文件的输入输出，简称**文件I/O**。

## 2.1 标准输入流
标准输入流对象cin，重点掌握的函数：
cin.get() //一次只能读取一个字符
cin.get(一个参数) //读一个字符
cin.get(两个参数) //可以读字符串，不会将换行符拿走，留在了缓冲区中
cin.getline() //读取一行数据，将换行符拿走
cin.ignore()  //参数为空的时候，默认忽略1，参数为n时，忽略n个字符
cin.peek()	  //偷窥
cin.putback() //放回

> 小案例：设计一个函数，让用户输入0-9，如果输入错误，重新输入

```
void test01()
{
	cout << "请输入0-9：" << endl;
	int num;
	while (true)
	{
		cin >> num;
		if (num >= 0 && num <= 9)
		{
			cout << "输入正确：" << num << endl;
			break;
		}
		cout << "请重新输入0-9：" << endl;
		//重置标志位
		cin.clear();
		//清除缓冲区
		cin.sync();
		//cout << "标志位：" << cin.fail() << endl;
	}
}
```
注意：需要重置标志位cin.clear()，清除缓冲区cin.sync()，不然标志位被破坏，导致程序死循环。cin.fail()可以查看标志位，0表示正常，1表示不正常。

## 2.2 标准输出流
字符输出
cout.put()   //向缓冲区写字符
cout.write() //从buffer中写num个字节到当前输出流中。
```
void test02()
{
	cout.put('a').put('b').put('\n');//可以链式输出
	char buff[1024] = "hello world";
	cout.write(buff,strlen(buff));
}
```
通过流成员函数，格式化输出
```
void test03()
{
	int number = 99;

	cout.width(20);//宽度为20
	cout.fill('*');//空白填充*
	cout.setf(ios::left);//设置左对齐
	cout.unsetf(ios::dec); //卸载十进制
	cout.setf(ios::hex);//安装8进制
	cout.setf(ios::showbase);//强制输出八进制，0，0x打头
	cout.unsetf(ios::hex);//卸载8进制
	cout.setf(ios::oct);//安装16进制
	cout << number << endl;
}
```
运行结果：
{% asset_img 2.png 运行结果 %}

# 3 文件读写
输入输出是以系统指定的标准设备（输入设备为键盘，输出设备为显示器）为对象的。在实际应用中，常以磁盘文件作为对象。即从磁盘文件读取数据，将数据输出到磁盘文件。需要包含头文件`#include <fstream>`，这个头文件主要定义了三个类，ifstream，ofstream，fstream。由于文件设备并不像显示器屏幕与键盘那样是标准默认设备，所以它在fstream头文件中是没有像cout那样预先定义的全局对象，所以我们必须自己定义一个该类的对象。ifstream类，它是从istream类派生的，用来支持从磁盘文件的输入。ofstream类，它是从ostream类派生的，用来支持向磁盘文件的输出。fstream类，它是从iostream类派生的，用来支持对磁盘文件的输入输出。

文件输入输出方式设置值：
ios::out 以输出方式打开文件，如已有此名字的文件，则将其原内容全部清除。
ios::trunc 打开一个文件，如果存在，则将内容清除，如果不存在，则创建新文件。
ios::in 以默认的方式打开文件。

**09 文件的读写操作.cpp**
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std
//包含读写文件的头文件
#include <fstream>

void test01()
{

	ofstream outFile("./test.txt", ios::out | ios::trunc);
	//判断是否打开成功
	if (!outFile.is_open())
	{
		cout << "打开失败" << endl;
	}
	outFile << "name: seaworth" << endl;
	outFile << "age : 23" << endl;
	outFile << "id  : 001" << endl;
	//关闭文件
	outFile.close();
}

//读取文件
void test02()
{
	ifstream inFile;
	inFile.open("./test.txt", ios::in);

	if (!inFile.is_open())
	{
		cout << "打开失败" << endl;
	}
	
	//1 第一种方式
#if 0
	char buff[1024];
	while (inFile >> buff)
	{
		cout << buff << endl;
	}
#endif
	//2 第二种方式
#if 0
	char buff2[1024];
	while (!inFile.eof())//eof读到文件尾
	{
		inFile.getline(buff2, sizeof(buff2));
		cout << buff2 << endl;
	}
#endif
	//3 第三种方式(不推荐)
	char c;
	while ((c = inFile.get()) != EOF)
	{
		cout << c;
	}
	inFile.close();
}
//函数入口
int main()
{
	//test01();
	test02();

	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
运行结果：
{% asset_img 3.png 运行结果 %}



