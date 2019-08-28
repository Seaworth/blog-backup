---
title: MATLAB绘制模糊规则图形
date: 2019-08-28 21:25:48
toc: true
tags:
	- MATLAB
---
> 根植于内心的修养；无需提醒的自觉；以约束为前提的自由；为别人着想的善良。——十点读书<!-- more -->

今天在写论文的时候，需要将模糊控制中的模糊规则和模糊曲面绘制出来。在网上查找了官方文档，将其总结下来。
在MATLAB中，已经设计好的模糊推理系统文件为`fuzzy_PID.fis`。
Display fuzzy inference system的代码如下：
```
clc
clear
close all

% 读取模糊规则
fis = readfis('fuzzy_PID.fis');
figure
plotfis(fis)%figure1绘制模糊的结构框图
figure
plotmf(fis,'input',1)%figure2输入1的规则
figure
plotmf(fis,'input',1)%figure3输入2的规则
figure
plotmf(fis,'output',1)%figure4输出1的规则
figure
gensurf(fis)%figure5模糊曲面
```
输出结果为：
<img src="f1.png" width="50%" height="50%" alt="figure1绘制模糊的结构框图"/>

<img src="f2.png" width="50%" height="50%" alt="figure2输入1的规则"/>

<img src="f3.png" width="50%" height="50%" alt="figure3输入2的规则"/>

<img src="f4.png" width="50%" height="50%" alt="figure4输出1的规则"/>

<img src="f5.png" width="50%" height="50%" alt="figure5模糊曲面"/>

