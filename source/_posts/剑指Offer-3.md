---
title: 剑指Offer-3 字符串中子序列的替换
date: 2019-09-10 10:21:18
toc: true
tags:
	- C++
	- 剑指
---
题目：查找字符串s中的子序列oldVal，并将oldVal替换为新的子序列newVal。<!-- more -->
# 题目描述
> 编写一个函数，接受三个string参数s、oldVal、newVal。使用迭代器及insert和erase函数将s中所有oldVal替换为newVal。

# 输出描述
> s="To drive straight thru is a foolish, tho courageous act."
oldVal="thru"
newVal="through"
oldVal="tho"
newVal="though"
替换后
To drive straight through is a foolish, though courageous act.

# 思路
首先对字符串s进行遍历，判断是否与oldVal子串相同，如果相同则删除s中的oldVal，并在这个位置添加新的子串newVal，此时的迭代器增加newVal的大小，如果不相同，则遍历下一个，直到遍历到字符串s的长度减去oldVal子串的长度的位置，停止遍历。

# 代码
```
void replace_with(string& s, string const& oldVal, string const& newVal)//注意参数s一定要是引用，不然无法改变s的值
{
	auto cur = s.begin();
	while (cur <= ( s.end() - oldVal.size() ) )
	{
		string cmpVal(cur, cur + oldVal.size());
		if (oldVal == cmpVal)
		{
			cur = s.erase(cur, cur + oldVal.size());//删除旧子串
			cur = s.insert(cur, newVal.begin(), newVal.end());//添加新子串
			cur += newVal.size();//增加的步长为新子串的长度
		}
		else
		{
			++cur;//遍历下一个位置
		}
	}
}
```