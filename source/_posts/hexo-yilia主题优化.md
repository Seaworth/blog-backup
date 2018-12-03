---
title: hexo-yilia主题优化
date: 2018-11-29 20:51:52
toc: true
tags: 
	- hexo
	- yilia
---
为博客添加站点统计，设置网页标签图标ico，修改头像。<!-- more -->
# 1.添加站点统计
- [不蒜子][1]
打开**themes\yilia\layout\_partial\footer.ejs**添加以下脚本即可。
```
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<div calss="count-span">
	<span id="busuanzi_container_site_pv">
		本站总访问量<span id="busuanzi_value_site_pv"></span>次
	</span>
	<span id="busuanzi_container_site_uv">
		本站总访客数<span id="busuanzi_value_site_uv"></span>人
	</span>
	<span id="busuanzi_container_page_pv">
		本文总阅读量<span id="busuanzi_value_page_pv"></span>次
	</span>
```

{% asset_img 1.png This is an example image %}

# 2.设置网页标签图标
- 去[比特虫][2]做icon图标
- 把favicon.ico图片放到**F:\blog\public\img**文件夹下面
- 找到**F:\blog\themes\yilia\layout\_partial\head.ejs**，设置为
```
<% if (theme.favicon){ %>
    <link rel="icon" href="/img/favicon.ico">
  <% } %>
```

# 3.修改头像

- 将avater.jpg头像保存在**F:\blog\public\img**中
- 打开**F:\blog\themes\yilia\_config.yml**，设置为
```
#你的头像url
avatar: "/img/avater.jpg"
```
# 4.最终效果

{% asset_img 2.png This is an example image %}

[1]:http://ibruce.info/2015/04/04/busuanzi/
[2]:http://www.bitbug.net/