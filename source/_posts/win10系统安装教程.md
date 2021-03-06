---
title: win10系统安装教程
date: 2018-03-04 21:15:14
toc: true
tags:
	- win10安装
	- 教程
---
### 1、前言
在这个信息爆炸的时代，几乎人人都有一台电脑，有时候电脑用久了很卡，或是出现莫名其妙的问题，你就会想到重新安装系统。每次重装系统都叫别人帮忙总显得麻烦，不如自己动手，丰衣足食。安装系统成为一种必备的技能。其实安装系统并没有想象的那么难，曾经的我也是一个小白，大一买来电脑生怕出问题，害怕把电脑装坏了。还好寝室有个电脑高手，看着他经常装系统，从Windows的xp到win10，ubuntu，最好玩了的是把联想笔记本装成了苹果系统，从此有了一台“黑苹果”，跟着室友我也会学会装系统了哈哈，只要不嫌麻烦，不断折腾，总会安装好的。在这里给大家分享一种安装系统的方法，用UltraISO（俗称软碟通）把Win10 ISO镜像写入U盘制作系统启动盘，再在bios界面设置U盘启动，然后一路回车，最后会遇见你的win10！<!-- more -->

### 2、准备

* 一个大于4G的U盘 
  >注意提前备份好U盘上的文件，制作启动盘时会格式化里面的文件
  
* UltraISO（俗称软碟通）软件 
  >百度下载一个即可
  
* Win10 ISO镜像 
  >给大家推荐一个[下载Windows系统的网址][1]，在百度上搜索win10系统下载，很多都不是微软原版的，并集成了很多常用软件或垃圾软件进去，MSDN网站里是正版纯净的Windows系统，MSDN是微软面向软件开发者所建的一个网站，它里面的资源很多，包括操作系统和Office软件。
 
注意下载操作系统前，先查看自己的电脑是32位还是64位，下载对应的操作系统，保证系统和电脑硬件匹配以发挥最好的性能，x86对应32位，x64对应64位。

{% asset_img 1.png This is an example image %}

### 3、制作启动盘
打开UltraISO（俗称软碟通）软件，界面如下

{% asset_img 2.png This is an example image %}

按照截图操作即可
- 1.打开win10镜像文件ios<br>
{% asset_img 3.png This is an example image %}
- 2.选中要制作启动盘的U盘<br>
{% asset_img 4.png This is an example image %}
- 3.启动->写入硬盘映像<br>
{% asset_img 5.png This is an example image %}<br>
{% asset_img 6.png This is an example image %}

刻录成功后，打开资源管理器，你的U盘会变成如下样式，代表着启动盘制作成功了。

{% asset_img 7.png This is an example image %}

到这里已经成功了一半了。
### 4、在bios下设置U盘启动
将制作好的U盘插入将要安装的电脑上，根据你电脑的品牌型号，在网上搜索怎样进入bios界面，设置U盘启动。我的电脑是台式惠普，开机时按住F9进入bios界面，选择启动盘SanDisk，回车进入安装界面。

{% asset_img 8.png This is an example image %}

{% asset_img 9.png This is an example image %}

然后根据提示一路回车，你就会遇见你的win10！


  [1]: https://msdn.itellyou.cn/
