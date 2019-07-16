---
title: Effective C++ 笔记
date: 2019-07-16 09:10:08
toc: true
tags:
	- C++
---
> 你的时间有限，所以不要浪费时间去重复别人的生活。
> Your time is limited, so don't waste it living someone else's life.
> ——斯蒂夫.乔布斯<!-- more -->


# 条款01：视C++为一个语言联邦
比较简单的方法是将C++视为一个由相关语言组成的联邦而非单一语言。C++主要由4个联邦组成：
- C。对内置类型而言，pass-by-value通常比pass-by-reference高效。C的局限：没有模板，没有异常，没有重载。
- Object-Oriented C++。对于用户自定义的类型，有构造函数和析构函数的存在，pass-by-reference-to-const效果更好。
- Template C++。同Object-Oriented C++一样。
- STL。在STL中，迭代器和函数对象是再C指针的基础上塑造起来的，所以对STL的迭代器和函数对象而言，pass-by-value守则再次适用。

# 条款02：尽量以const，enum，inline替换#define

- 对于单纯的变量，最好以const对象或enum替换#define
- 对于形式像函数的宏，最好改用inline函数特换#define

# 条款03：尽可能使用const
- 将某些东西声明为const可帮助编译器侦测出错误的用法。const可被施加于任何作用域内的对象、函数参数、函数返回类型、成员函数本体。
- 当const和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本可避免代码重复。

const语法虽然变化多端，但并不莫测高深。如果关键字const出现在星号左边，表示被指物是常量；如果出现在星号右边，表示指针自身是常量；如果出现在星号两边，表示被指物和指针两者都是常量。

在const函数中想要改变某些成员变量的方法是：利用C++的一个与const相关的摆动场：mutable（可变的）。mutable释放掉non-static成员变量的bitwise constness约束。

# 条款04：确定对象被使用前已先被初始化
- 为内置型对象进行手工初始化，因为C++不保证初始化他们。
- 构造函数最好使用成员初值列，而不要在构造函数本体内使用赋值操作。初值列列出的成员变量，其排列次序应该和他们在class中的声明次序相同。
- 为免除“跨编译单元之初始化次序”问题，请以local static对象提花non-local static对象。

# 条款05：了解C++默默编写并调用了哪些函数
- 编译器可以暗自为class创建default构造函数、copy函数、copy assignment操作符，以及析构函数。


# 条款06：若不想使用编译器自动生成的函数，就该明确拒绝
- 为驳回编译器自动提供的机能，可将相应的成员函数声明为private并且不予实现。使用向Uncopyable这样的base class也是一种做法


``` C++
//方法一
class HomeForSale
{
public:
    //...
private:
    //将copy函数和copy assignment函数设置为私有属性
    HomeForSale(const HomeForSale&);
    HomeForSale& operator=(const HomeForSale&);
};
```
```C++
//方法二
class Uncopyable
{
protected:
    Uncopyable(){}
    ~Uncopyable(){}
private:
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(cosnt Uncopyable&); 
};
//继承Uncopyable类
//从而阻止HomeForSale对象被拷贝
class HomeForSale: private Uncopyable
{
    //...
}
```
# 条款07：为多态基类声明virtual析构函数
- polymorphic（带多态性质的）base classes应该声明一个virtual析构函数。如果class带有任何virtual函数，它就应该拥有一个virtual析构函数。
- classes的设计目的如果不是作为base class来使用，或不是为了具备多态性，就不该声明virtual析构函数。

# 条款08：别让异常逃离析构函数
- 析构函数绝对不要吐出异常。如果一个析构函数调用的函数可能抛出异常，析构函数应该捕捉任何异常，然后吞下他们（不传播）或结束程序。
- 如果客户需要对某个操作函数运行期间抛出的异常做出反应，那么class应该提供一个普通函数（而不是在析构函数中）执行该操作。

# 条款09：绝不在构造和析构过程中调用virtual函数
- 在构造和析构期间不要调用virtual函数，因为这类调用从不下降至派生类，因为“派生类中的成员变量处于未定义状态”，所以“在基类构造和析构函数期间调用virtual函数不可下降至派生类”。

# 条款10：让operator=返回一个reference to *this
- 这样能够实现“连等”

```C++
class Widget
{
public:
    //...
    Widget& operator= (const Widget& rhs)
    {
        //...
        return *this;
    }
}
```

# 条款11：在operator=中处理“自我赋值”
- 确保当对象自我赋值时operator=有良好的行为。其中技术包括比较“来源对象”和“目标对象”的地址、精心周到的语句顺序、以及copy-and-swap。
- 确定任何函数如果操作一个以上的对象，而其中多个对象是同一个对象时，其行为仍然正确。
* 方法一

```C++
class Bitmap{}
class Widget
{
    //...
private:
    Bitmap* pb;
}

Widget& Widget::operator=(const Widget &rhs)
{
    if(this == &rhs) return *this; //如果是自我赋值，不做任何事
    
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return *this;
}
```

* 方法二

```C++
Widget& Widget::operator=(const Widget &rhs)
{
    Bitmap *pOrig = pb; //记住原来的pb
    pb = new Bitmap(*rhs.pb);//令pb指向*pb的一个副本
    delete pOrig;//删除原先的pb
    return *this;
}
```

* 方法三

```C++
class Widget
{
    //...
    void swap(Widget &rhs);//交换*this和rhs的数据
    //...
}

Widget& Widget::operator=(const Widget &rhs)
{
    Widget temp(rhs);//为rhs数据制作一个副本
    swap(temp);//将*this数据和上述副本的数据交换
    return *this;
}
```
* 方法四
```C++
//将复制的操作转移到了“函数参数构造的阶段”
Widget& Widget::operator=(Widget rhs)//采用pass by value
{
    swap(temp);//将*this数据和副本的数据交换
    return *this;
}
```

# 条款12：复制对象时勿忘记每一个成分
- Copying函数应该确保复制“对象内的所有成员变量”及“所有base class成分”。
- 不要尝试以某个copying函数实现另外一个copying函数。应该将共同机能放进第三个函数，并由两个Coping函数共同调用。

>所谓资源就是，**一旦用了它，将来必须还给系统**。

>**堆**：程序的运行场所是内存，栈和堆是进程的虚拟内存中的两部分区域。当程序被执行，所创建的常量、变量等都会被压入栈空间中。堆是不同于栈的另一个区域，系统会给每个程序分配一部分堆空间，让他们能够运行起来。堆不属于程序，堆是独立的、公用的。栈里的东西有生命周期，就是变量的作用域，而堆里的东西独立于程序，molloc()之后，除非free()掉，否则一直都存在，会造成内存泄漏。

# 条款13：以对象管理资源

- 获得资源后立刻放进管理对象内
- 管理对象运行析构函数确保资源释放
>以对象管理资源的观念：资源取得的时机便是初始化时机。（Resource Acquisition Is Initialization; RAII）

1. 为防止资源泄漏，请使用RAII对象，他们在构造函数中获得资源并在析构函数中释放资源。
2. 两个常被使用的RAII class是tr1::shared_ptr和auto_ptr。前者通常是较佳选择，因为其copy行为比较直观。若选择auto_ptr，复制动作会使它指向null。

# 条款14：在资源管理类中小心copying行为
- 复制RAII对象必须一并复制它所管理的资源，所以资源的copying行为决定了RAII对象的行为。
- 常见RAII class copying行为是：抑制copying行为、施行引用计数法。不过其他行为也都是可能被实现的。

# 条款15：在资源管理类中提供对原始资源的访问
- APIs往往要求访问原始资源，所以每一个RAII class应该提供一个“取得其所管理的资源”的方法。
- 对原始资源的访问可能经由显示转换或隐式转换。一般而言显式转换比较安全，隐式转换对客户比较方便。


# 条款16：成对使用new和delete时要采取相同的形式
- 如果你在new表达式中使用了[ ]，必须在delete表达式中也使用[ ]，如果你在new表达式中没有使用[ ]，一定不要在delete表达式中使用[ ]。

# 条款17：以独立语句将newed指针置入智能指针
- 如果不这样做，一旦异常被抛出，有可能导致难以察觉的资源泄漏
```C++
processWidget(std::tr1::shared_ptr<Widget>(new Widget), priority() );
//可能造成资源泄漏，
//因为电泳processWidget之前，编译器必须创建代码
/*
1. 执行“new Widget”
2. 调用priority函数
3. 调用tr1::shared_ptr构造函数
万一调用priority导致异常，会使返回“new Widget”的指针遗失
*/

std::tr1::shared_ptr<Widget> pw(new Widget);
processWidget(pw, priority() );//这个调用不会造成资源泄漏
```
# 条款18：让接口容易被使用，不易被误用
- “促进正确使用”的方法包括接口的一致性，以及与内置类型的行为兼容
- “阻止误用”的方法包括建立新类型、限制类型上的操作，束缚对象值，以及消除客户的资源管理责任
- tr1:::shared_ptr支持定制型删除器。这可以防范DLL（动态链接库）问题，可被用来自动解除互锁等等

# 条款19：设计class犹如设计type
- 新type的对象应该如何被创建和销毁？
- 对象的初始化和对象的复制有什么差别？
- 新type对象如果被值传递，意味着什么？
- 什么是新type的“合法值”？
- 新type需要配合某个继承图系么？
- 新type需要什么样的转换？
- 什么样的操作符和函数对新type而言是合理的？
- 什么样的标准函数应该被驳回？
- 谁该取用新type的成员？
- 你的新type有多么一般化？
- 你真的需要一个新的type么？











