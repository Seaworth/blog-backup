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


