---
title: 剑指Offer-2
date: 2019-01-20 08:50:37
toc: true
tags:
	- C++
	- 剑指
---
题目：孩子们的游戏(圆圈中最后剩下的数)<!-- more -->
[在线编程][1]
# 题目描述
> 每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)

# 思路
模拟游戏的过程：规定数组中的值，1表示小朋友没有出列，-1表示已经出列。
1. 数组的所有值设为1
2. 两个计数器countn，countm，每循环一次countn++，但只有数组中的值为1的时候，countm++，为-1的时候，countm不变（就相当于数到被出列的小朋友时，countm不计数）
3. 简单的示意图如下<br>
{% asset_img 绘图1.png This is an image %}

# 代码
```
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if((n<1)|(m<1)){return -1;}//数据不规范
        
        int person[n];//创建长度为n的数组，表示n小朋友
        for(int i=0;i<n;i++)
        {
            person[i]=1;//1表示小朋友还在环内，-1表示出局
        }
        int countn=-1,countm=0,i=0;
        while(i<n)
        {
            countn++;
            if (countn>=n){countn=0;}//模拟环，数到最后一个小朋友
            if(person[countn]==-1){continue;}//这个小朋友已出局，跳过，不记录countm
            countm++;
            if(countm==m)
            {
                countm=0;
                i++;
                person[countn]=-1;//小朋友出局
            }
        }
        return countn;
    }
};
```
# 结束语
> 青山不改，流水长流！加油吧，少年！

[1]:https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=3&rp=3&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking