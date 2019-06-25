---
title: Qt day01 初识Qt
date: 2019-06-24 21:06:05
toc: true
tags:
	- Qt
	- C++
---
> 历史上每一个伟大的权威时刻都是热情的胜利。——艾默生<!-- more -->

学习完C++的基础教程，再学习Qt，让程序有一个漂亮的UI。

# 1 Qt概述
## 1.1 Qt是什么？
Qt是一个**跨平台**的C++**图形用户界面应用程序框架**。它为应用程序开发者提供建立艺术级图形界面所需的所有功能。它是完全面向对象的，很容易扩展，并且允许真正的组件编程。

## 1.2 Qt支持的平台

- Windows：XP、Vista、Win7、Win8、Win10
- Linux
- Mac OS X
- Embedded：有帧缓冲支持的嵌入式Linux平台

## 1.3 Qt的优点

- 跨平台，几乎支持所有的平台
- 接口简单，容易上手，学习QT框架对学习其他框架有参考意义
- 一定程度上简化了内存回收机制 
- 开发效率高，能够快速的构建应用程序
- 有很好的社区氛围，市场份额在缓慢上升
- 可以进行嵌入式开发

用Qt开发的，比较成功的产品有：Linux的桌面环境KDE、WPS Office办公软件、VirtualBox虚拟机软件


# 2 Qt项目中的.pro文件
```
QT       += core gui #包含的模块

greaterThan(QT_MAJOR_VERSION, 4): QT += widgets #大于QT4版本，才包含widgets模块

TARGET = myFirstQt #应用程序名，生成exe程序名称
TEMPLATE = app #模板类型，应用程序模板
DEFINES += QT_DEPRECATED_WARNINGS 
CONFIG += c++11 #使用c++11的特性

SOURCES += \ #源文件
        main.cpp \
        widget.cpp

HEADERS += \ #头文件
        widget.h
```
.pro就是工程文件（project），它是qmake自动生成的用于生产makefile的配置文件。.pro文件的写法如下：
- 注释
从“#”开始，到这一行结束。
- 模板变量告诉qmake为这个应用程序生成哪种makefile。TEMPLATE = app，建立一个应用程序的makefile，这是默认值。
- greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
这条语句的含义是，如果QT_MAJOR_VERSION大于4（也就是当前使用的Qt5及更高版本）需要增加widgets模块。如果项目仅需支持Qt5，也可以直接添加“QT += widgets”一句。不过为了保持代码兼容，最好还是按照QtCreator生成的语句编写。

# 3 一个简单的Qt程序
main入口函数。
```
#include "widget.h"
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Widget w;
    w.show();

    return a.exec();
}
```
解释：
- Qt系统提供的标准类名声明头文件没有.h后缀。
- Qt一个类对应一个头文件，类名就是头文件名。
- QApplication应用程序类。管理图形用户界面应用程序的控制流和主要设置，是Qt的整个后台管理的命脉它包含主事件循环，在其中来自窗口系统和其它资源的所有事件处理和调度。它也处理应用程序的初始化和结束，并且提供对话管理。对于任何一个使用Qt的图形用户界面应用程序，都正好存在一个QApplication对象，而不论这个应用程序在同一时间内是不是有0、1、2或更多个窗口。
- a.exec()。程序进入消息循环，等待对用户输入进行响应。这里main()把控制权转交给Qt，Qt完成事件处理工作，当应用程序退出的时候exec()的值就会返回。在exec()中，Qt接受并处理用户和系统的事件并且把它们传递给适当的窗口部件。

# 4 信号与槽机制
> 信号槽是Qt 框架引以为豪的机制之一。所谓信号槽，实际就是观察者模式。当某个事件发生之后，比如，按钮检测到被点击了一下，它就会发出一个信号（signal）。这种发出是没有目的的，类似广播。如果有对象对这个信号感兴趣，它就会使用连接（connect）函数，意思是，将想要处理的信号和自己的一个函数（slot）绑定来处理这个信号。也就是说，当信号发出时，被连接的槽函数会自动被回调。这就类似观察者模式：当发生了感兴趣的事件，某一个操作就会被自动触发。

## 4.1 系统自带的信号与槽
connect()函数最常用的一般形式：
```
connect(sender, signal, receiver, slot);
```
参数解释：
sender：发出信号的对象
signal：发送对象发出的信号
receiver：接收信号的对象
slot：接收对象在接收到信号之后所需要调用的函数（槽函数）

## 4.2 Lambda表达式
C++11中的Lambda表达式用于定义并创建匿名的函数对象，以简化编程工作。
```
[capture](parameters) mutable -> return-type
{
	statement
}
//[函数对象参数](操作符重载函数参数)mutable ->返回值{函数体}
```

[]，标识一个Lambda的开始，这部分必须存在，**不能省略**。函数对象参数是传递给编译器自动生成的函数对象类的构造函数的。函数对象参数只能使用那些到定义Lambda为止时，Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。函数对象参数有以下形式：
- 空。没有使用任何函数对象参数。
- =。函数体内可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**值传递方式**（相当于编译器自动为我们按值传递了所有局部变量）。这种方式使用的比较多。
- &。函数体内可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**引用传递方式**（相当于编译器自动为我们按引用传递了所有局部变量）。
- this。函数体内可以使用Lambda所在类中的成员变量。
- mutable声明，这部分可以省略。按值传递函数对象参数时，加上mutable修饰符后，可以修改按值传递进来的拷贝（注意是能修改拷贝，而不是值本身）。





