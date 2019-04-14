---
title: C++ day02
date: 2019-02-26 19:33:52
toc: true
tags:
	- C++
---
> 人生中最大的两个财富是：你的**才华**和你的**时间**。
才华越来越多而时间越来越少。我们的一生就是用时间来换取才华。<!-- more -->

# 1 extern C浅析——C++中调用C
在VS2010中新建工程`06 extern C浅析`，添加三个新建项
- `test.h`
- `test.c`
- `06 extern C浅析.cpp`
## 1.1 在C语言代码中处理
```
#ifdef __cplusplus //两个下划线__
extern "C" {
#endif

	//一些用C语言实现的声明
	void test();

#ifdef __cplusplus //两个下划线__
}
#endif
```
> 上面代码的含义是如果是C++编译器对C语言实现的test函数进行编译，则按照C语言编译，如果不是C++编译器则按照C语言正常处理。
如果要声明C语言的函数较多时，采用这种方法。

`test.h`
```
#pragma once //防止头文件重复编译

#ifdef __cplusplus //两个下划线__
extern "C" {
#endif

	#include <stdio.h>//包含基本的输入输出，C语言
	void show();
	void show1();
	void show2();

#ifdef __cplusplus //两个下划线__
}
#endif
```

`test.c`
```
#include "test.h"

void show()
{
	printf("hello world\n");
}
void show1()
{
	printf("hello world1\n");
}
void show2()
{
	printf("hello world2\n");
}
```

`06 extern C浅析.cpp`
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std
#include "test.h"

//C++中调用C语言方法
//extern "C" void show();//show方法，按照C语言方式做连接
//解决问题：在C++中调用C语言的函数

//函数入口
int main()
{
	show();//C++中函数是可以发生重载，编译器会把这个函数名称偷偷改变
	show1();
	show2();
	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```

## 1.2 在C++语言代码中处理
```
//C++中调用C语言方法
extern "C" void show();//show方法，按照C语言方式做连接
//解决问题：在C++中调用C语言的函数
```
> 调用C语言实现的函数较少时，可采用这种方法。

`test.h`
```
#pragma once //防止头文件重复编译

#include <stdio.h>//包含基本的输入输出，C语言
void show();
```

`test.c`
```
#include "test.h"

void show()
{
	printf("hello world\n");
}
```

`06 extern C浅析.cpp`
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream> //标准输入输出流
using namespace std;//使用命名空间 std

//C++中调用C语言方法
extern "C" void show();//show方法，按照C语言方式做连接
//解决问题：在C++中调用C语言的函数

//函数入口
int main()
{
	show();//C++中函数是可以发生重载，编译器会把这个函数名称偷偷改变

	system("pause");//阻塞功能
	return EXIT_SUCCESS;//返回正常
}
```
# 2 内联函数
内联函数是为了解决宏定义的缺陷。
注意事项：
1. 内敛只是给编译器的一个小建议，加了inline编译器也不一定进行内联操作
2. 没有进行inline声明，编译器也有可能优化，帮你进行内联编译
3. 类的成员函数，编译器会自动添加inline

# 3 函数重载
> 默认参数：如果第一个位置有了默认参数，后面的必须要有默认参数。

函数重载的注意事项：
1. 必须要在同一作用域下
2. 函数的**个数不同**，**参数类型不同**，**参数的顺序不同**
3. **函数的返回值不可以作为重载的条件**
4. const 可以作为重载的条件

# 4 C++的封装
> C语言的封装：属性和行为分开处理，类型检测不够强。
C++语言的封装：属性和行为绑在一起，严格的类型转换检测。
1. 属性和行为作为一个整体来表示生活中的事物
2. 具有三种控制权限：public，protected，private
3. C++中struct和class是一个意思，但是struct的默认权限是`public`，class的默认权限是`private`

总结：
- public，类的内部和外部都可以访问
- protected，类的内部可以访问，类的外部不可以访问，子类可以访问
- private，类的内部和外部都不可以访问，子类也不可以访问
- 建议类的成员属性设置为`private`


