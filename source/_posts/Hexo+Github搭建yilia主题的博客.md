---
title: Hexo+Github搭建yilia主题的博客
date: 2018-02-04 14:45:01
toc: true
tags:
	- hexo
	- yilia
---
### 1 概述
什么是Hexo？

> [Hexo](https://hexo.io/zh-cn/docs/index.html) 是一个快速、简洁且高效的博客发布工具。Hexo支持Markdown格式，众多优秀插件和主题。在几秒内，即可利用靓丽的主题生成静态网页。
<!-- more -->

什么是Github？

> Github是一个面向开源及私有软件项目的托管平台，因为只支持git 作为唯一的版本库格式进行托管，故名Github。

什么是Git？

> Git是目前世界上最先进的分布式版本控制系统。

### 2 准备
我是在win10环境下配置的，搭建博客需要以下的准备工作：
- Github账号
作用：是用来做博客的远程仓库，[Github官网][1]申请一个账户，每一个Github账户（如我的账户名为Seaworth））对应有一个域名（则[我的博客地址][2] https://seaworth.github.io/）。
- Git软件安装
作用：把本地的hexo内容提交到github上去，[Git软件官网下载][3]，安装即可。
- Node.js安装
作用：用来创建hexo博客框架的，[Node.js官网下载][4]，安装即可。

在Git Bash中验证上述软件是否安装成功，如果能够出现以下版本信息，则说明安装成功。
``` bash
$ git version # git的版本信息
git version 2.8.1.windows.1
$ node -v # node的版本信息
v8.9.4
```
如果您的电脑上已经安装上述必备程序，那么恭喜您！接下来只需要使用 npm 即可完成 Hexo 的安装。
- Hexo安装
在Git Bash中输入以下命令安装 Hexo

``` bash
$ npm install -g hexo-cli
```
检查是否安装成功，出现版本信息，安装成功。
``` bash
$ hexo -v
hexo-cli: 1.0.4
os: Windows_NT 10.0.16299 win32 x64
http_parser: 2.7.0
node: 8.9.4
v8: 6.1.534.50
uv: 1.15.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.25.0
openssl: 1.0.2n
icu: 59.1
unicode: 9.0
cldr: 31.0.1
tz: 2017b
```
### 3 建站
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。我是在F磁盘新建文件夹hexo，这个hexo文件夹中将存放整个博客的文章和网站的配置文件等，可根据自己的需要更改。
在hexo文件夹下右键打开Git Bash命令行
``` bash
$ hexo init  #将blog初始化
```
hexo会自动下载一些文件到这个目录，包括node_modules，目录结构如下图：
{% asset_img QQ截图20180203150040.png This is an example image %}
``` bash
$ hexo g  # 也可以写成hexo generate，生成静态文件
$ hexo s  # 也可以写成hexo server，启动服务器
```
执行以上命令之后，hexo文件夹中会增加两个文件，其中一个public文件夹生成相关html文件：
{% asset_img QQ截图20180202153116.png This is an example image %}
{% asset_img QQ截图20180203151320.png This is an example image %}
{% asset_img QQ截图20180203150931.png This is an example image %}

hexo s是开启本地预览服务，打开浏览器访问 http://localhost:4000 即可看到如下页面：
{% asset_img QQ截图20180203151852.png This is an example image %}

第一次初始化的时候hexo已经帮我们写了一篇名为 Hello World 的文章，这是默认的主题landscape。
### 4 配置yilia主题
默认主题不是很好看，我们就来换个好看点的主题，这里是[官方主题][10]
我比较喜欢的两个主题是[hexo-theme-yilia][11]和[hexo-theme-next][12]，我选择的yilia主题，比较喜欢他的风格，崇尚简约优雅。
在hexo文件夹下右键打开Git Bash命令行
``` bash
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
下载后，themes文件夹下会多一个yilia文件夹
{% asset_img QQ截图20180203154359.png This is an example image %}

然后，在[yilia主题的博客备份][14]中的_config.yml配置文件中，复制里面的全部内容，粘贴到在hexo文件夹下的_config.yml文件。
在hexo文件夹下右键打开Git Bash命令行
``` bash
$ hexo g
$ hexo s
```
打开浏览器访问 http://localhost:4000 ，即可看到更改为yilia主题的页面
{% asset_img QQ截图20180203161438.png This is an example image %}

此时就只需要更改hexo文件夹下的_config.yml配置文件和yilia文件夹下的_config.yml配置文件，yilia主题的作者[Litten][16]将配置文件的备注写的比较清楚，不过作为小白的我还是改了一些时间，也遇到一些问题，分享给大家，避免的大家也在类似的地方卡住。
hexo文件夹下的_config.yml配置文件

``` bash
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://seaworth.github.io/  # 这里是自己博客的地址，但要以http开头，不是https
root: /
permalink: :year/:month/:day/:title/
```
yilia文件夹下的_config.yml配置文件
``` bash
# 支付宝二维码图片地址，跟你设置头像的方式一样。比如：/assets/img/alipay.jpg
alipay: "/img/alipay.jpg"
```
更改支付宝赞赏的图片的时候，是以yilia文件下的source文件为根目录，所以将alipay.jpg图片放到source/img/目录下后，在对应位置输入"/img/alipay.jpg"即可，注意alipay: 冒号后面有空格。

### 5 发布到Github
此时还只能够在本地的浏览器看自己的博客，当我们把本地的hexo博客网页配置和博客文章推送到Github上，生成静态网页，所有人都可以访问博客了。
#### 5.1 创建repository
repository相当于一个仓库，用来放置代码文件。首先，登陆进入[Github][17]，并进入个人页面，然后New一个repository
{% asset_img QQ截图20180203164834.png This is an example image %}

创建时，只需要填写Repository name即可，当然这个名字的格式必须为yourname.github.io，例如我的为[Seaworth.github.io][19]
然后编辑F:/hexo下的_config.yml文件，建议使用Notepad++编辑器。将deploy部分修改成自己的信息

``` bash
deploy:
  type: git
  repo: git@github.com:Seaworth/Seaworth.github.io.git
  branch: master
```
repo后面填写以下红色方框中的内容
{% asset_img QQ截图20180203170735.png This is an example image %}

#### 5.2 配置SSH keys
为什么要配置这个呢？因为你提交代码肯定要拥有你的Github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。
在Git Bash中执行如下命令：
``` bash
$ cd ~/. ssh #检查本机已存在的ssh密钥
```
如果提示：No such file or directory 说明你是第一次使用git。

``` bash
ssh-keygen -t rsa -C "你的邮件地址"
```
然后连续3次回车，最终会在C:\Users\Administrator\.ssh\目录下生成三个文件，找到id_rsa.pub文件，记事本打开并复制里面的内容
{% asset_img QQ截图20180203172124.png This is an example image %}

打开你的github主页，点击头像->Settings-> SSH and GPG keys -> New SSH key：
{% asset_img QQ截图20180203172738.png This is an example image %}

将刚复制的内容粘贴到key那里，title随便填。
测试一下SSH是否配置成功，输入以下命令：
``` bash
$ ssh -T git@github.com
```
你可能会看到有警告，输入“yes”就好，然后会看到

> Hi Seaworth! You've successfully authenticated, but GitHub does not provide shell access.

有这个信息说明SSH已配置成功！

#### 5.3 发布
执行以下命令发布到到Github上。

``` bash
$ hexo g 
$ hexo d 
```
如果执行hexo d命令报错，就先安装一下hexo-deployer-git这个模块：
``` bash
$ npm install hexo-deployer-git --save
```
安装好了继续执行hexo d部署命令，输入gitHub的账号密码，就可以访问了。

### 6 使用hexo写博客
Hexo使用Markdown解析文章，我是使用的[小书匠][23]在线编辑器+印象笔记，编写的文章直接同步到印象笔记挺方便的。
hexo常用的命令，#后面为注释。
``` bash
$ hexo n #完整命令为hexo new,用于新建一篇文章
$ hexo g #完整命令为hexo generate,用于生成静态文件
$ hexo s #完整命令为hexo server,用于启动服务器，主要用来本地预览
$ hexo d #完整命令为hexo deploy,用于将本地文件发布到github上
```
定位到我们的hexo根目录，执行命令：
``` bash
$ hexo n "hello my first blog" # 新建一篇文章名为hello my first blog
INFO  Created: F:\blog\source\_posts\hello-my-first-blog.md
```
Hexo会帮我们在F:\blog\source\_posts下生成相关md文件，打开这个文件就可以开始写博客了，Markdown语法编写，默认生成如下内容：
{% asset_img QQ截图20180203175708.png This is an example image %}

在后面添加##我的第一篇博客，啦啦啦！，写完文章后，在hexo文件夹下打开Git Bash输入
``` bash
$ hexo g
$ hexo d
```
然后使用 https://yourname.github.io 进行访问。
{% asset_img QQ截图20180203180907.png This is an example image %}

到这里就差不多搭建好了一个属于自己的博客了，还有一些细节的东西（如访问统计）可以后面慢慢完善，后面就只是发布文章了，这是我第一次写博客，有些地方可能说的不是很全面，搭建的过程中难免会遇到一些问题，在百度上搜索大部分都能够解决，只要怀着一颗折腾到底的心，相信一定能够搭建好属于自己的个人博客的！
### 7 参考
[使用hexo+github搭建免费个人博客详细教程][26]
[hexo干货系列：（一）hexo+gitHub搭建个人独立博客][27]

  [1]: https://github.com/
  [2]: https://seaworth.github.io/
  [3]: https://git-scm.com/download/win
  [4]: https://nodejs.org/en/
  [10]: https://hexo.io/themes/
  [11]: https://github.com/litten/hexo-theme-yilia
  [12]: https://github.com/iissnan/hexo-theme-next
  [14]: https://github.com/litten/BlogBackup/blob/master/_config.yml
  [16]: http://litten.me/
  [17]: https://github.com/
  [19]: https://github.com/Seaworth/Seaworth.github.io
  [23]: http://soft.xiaoshujiang.com/
  [26]: http://blog.haoji.me/build-blog-website-by-hexo-github.html?from=xa
  [27]: http://tengj.top/2016/02/22/hexo1/
