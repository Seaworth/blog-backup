---
title: 剑指Offer-1 和为S的两个数字
date: 2019-01-18 20:38:54
categories:
toc: true
tags:
	- C++
	- 剑指
---
题目：和为S的两个数字<!-- more -->
[在线编程][1]

# 题目描述
> 输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

# 输出描述
> 对应每个测试案例，输出两个数，小的先输出。

# 思路
因为数组递增，采用两端紧逼法。设置头尾两个指针i和j。
1. 判断数组长度，长度小于2无法做乘法，直接返回数组
2. a[i]+a[j]>sum，尾指针j减一，减小两数和的值
3. a[i]+a[j]<sum，首指针i加一，增加两数和的值
4. a[i]+a[j]==sum，如果相等，直接返回，因为数组递增，相隔最远的两数乘积最小

# 代码
```
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        vector<int> result;
        int len=array.size();
        if(len<2){return array;}//数组长度小于2，无法两数做乘法
        //两端紧逼法
        int i=0;//左端下标
        int j=len-1;//右端下标
        int my_sum;
        while(i<j)
        {
            my_sum=array[i]+array[j];
            if (my_sum>sum)
            {
                j--;//右端下标减一
            }
            else if (my_sum<sum)
            {
                i++;//左端下标加一
            }
            else
            {
                result.push_back(array[i]);
                result.push_back(array[j]);
                break;//找到后直接退出
            }
        }
        return result;
    }
};
```
# 结束语
> 青山不改，流水长流！加油吧，少年！
[1]:https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=3&rp=3&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking
