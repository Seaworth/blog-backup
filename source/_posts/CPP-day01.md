---
title: C++ day01
date: 2018-12-16 21:37:07
tags:
	- C++
---
# 1.C++概述
C++是C的超集，是C的补充和扩展，兼容C。<!-- more -->
**C++和C的区别：**
- C：面向过程。（功能分解，自顶向下，逐层分解），程序=算法+数据结构
- C++：面向对象。（一切皆对象），对象=算法+数据结构，程序=对象+对象
	   
**面向对象三大特性：**
- 封装，“抽象”
- 继承
- 多态，“一个接口，多种方法”

# 2.namespace命名空间
- 用来解决命名冲突
- 必须在全局作用域下声明
- 命名空间下可以放函数、变量、结构体、类
- 命名空间可以嵌套命名空间
- 命名空间是开放的，随时可以添加新的成员
- 存在匿名空间

# 3.using声明和using编译
## 3.1 using声明
**using KingGlory::sunwukongID;**说明**sunwukongID**使用**KingGlory**下的。
编译器有就近原则，需要避免二义性。

## 3.2 using编译
```
//using编译指令
//就近原则 大于 打开房间
using namespace KingGlory;
using namespace LOL;
//如果打开多个房间，要避免二义性，明确指明变量的命名空间
cout << LOL::sunwukongID << endl;
```

