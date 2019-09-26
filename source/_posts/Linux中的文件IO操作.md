---
title: Linux中的文件IO操作
date: 2019-09-24 21:27:50
toc: true
tags:
	- Linux
---
> 回顾我自己已走过的历史，扪心自问，我一生无愧于祖国、无愧于人民，无愧于事业与员工，无愧于朋友，唯一有愧的是对不起父母，没条件时没有照顾他们，有条件时也没有照顾他们。——任正非《我的父亲母亲》<!-- more -->

# 1.文件概述
Linux中，一切皆文件。文件为操作系统服务和设备提供了一个简单而一致的接口。
文件IO主要包含5个函数，如下所示：
- open，打开或创建一个文件
- close，关闭一个文件描述符
- read，从打开的文件或设备里读数据
- write，向文件或设备写数据
- lseek，重新定位读/写文件偏移量

## open
在终端查看open函数的用法：
```
seaworth@ubuntu:~/Desktop/io$ man 2 open
```
调用open函数，需要包含的头文件如下：
```
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
```
函数的形式：
```
int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);
```
函数参数：
- pathname，文件名
- flags，访问模式的参数标志
	* 必选项：`O_RDONLY`, `O_WRONLY`, `O_RDWR`
	* 可选项：`O_APPEND`, `O_CREAT`, `O_NONBLOCK`（`O_APPEND`以追加的方式打开，`O_CREAT`创建文件，`O_CREAT`与`O_EXCL`一起使用，如果文件存在，则报错。）
- mode，权限位，以`mode&~umask`的方式生成，`umask`的值为`0002`。

返回值：
返回最小的可用文件描述符，失败返回-1，并且设置错误数字errno。

## close
在终端查看close函数的用法：
```
seaworth@ubuntu:~/Desktop/io$ man close
```
功能：关闭打开的文件描述符。
调用close函数，需要包含的头文件如下：
```
#include <unistd.h>
```
函数的形式：
```
int close(int fd);
```
函数参数：
* fd，打开的文件描述符

返回值：调用成功返回0，失败返回-1，并设置错误数字errono

## read
在终端查看read函数的用法：
```
seaworth@ubuntu:~/Desktop/io$ man read
```
调用close函数，需要包含的头文件如下：
```
#include <unistd.h>
```
函数的形式：
```
ssize_t read(int fd, void *buf, size_t count);
```
函数参数：
* fd，打开的文件描述符
* buf，缓冲区
* count，写入count个字节到缓冲区

返回值：
* 成功，返回读到的大小
* 失败，返回-1
* 0表示读到文件末尾

## write
在终端查看write函数的用法：
```
seaworth@ubuntu:~/Desktop/io$ man 2 write
```
调用write函数，需要包含的头文件如下：
```
#include <unistd.h>
```
函数的形式：
```
ssize_t write(int fd, const void *buf, size_t count);
```
函数参数：
* fd，打开的文件描述符
* buf，缓冲区
* count，从缓冲区写入count个字节到fd文件中

返回值：
* 成功，返回写了多少字节
* 失败，返回-1
* 0表示什么也没有写

## lseek
在终端查看lseek函数的用法：
```
seaworth@ubuntu:~/Desktop/io$ man 2 lseek
```
调用lseek函数，需要包含的头文件如下：
```
#include <sys/types.h>
#include <unistd.h>
```
函数的形式：
```
off_t lseek(int fd, off_t offset, int whence);
```
函数参数：
* fd，打开的文件描述符
* offset，偏移量
* whence
	* SEEK_SET文件开始位置
	* SEEK_CUR文件当前位置
	* SEEK_END文件结尾

返回值：
* 成功，返回当前位置到开始的长度
* 失败，返回-1，并设置errno

# 2.三个小实践
## 实现touch指令
创建`mytouch.c`文件
```
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

int main(int argc, char* argv[])
{
	if(argc!=2)
	{
		printf("./mytouch filename\n");
	}
	int fd = open(argv[1],O_RDONLY|O_CREAT,0666);
	close(fd);
	return 0;
}
```
编译生成可执行文件`mytouch`
```
seaworth@ubuntu:~/Desktop/io$ gcc mytouch.c -o mytouch
seaworth@ubuntu:~/Desktop/io$ ./mytouch 1.log
seaworth@ubuntu:~/Desktop/io$ ls -lrt
-rw-rw-r-- 1 seaworth seaworth  264 Sep 18 20:24 mytouch.c
-rw-rw-r-- 1 seaworth seaworth    0 Sep 26 20:59 1.log
-rwxrwxr-x 1 seaworth seaworth 8704 Sep 26 21:06 mytouch
```

## 实现cat功能
编写`mycat.c`文件，实现读取文件内容并且输出到屏幕上。
```
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>

int main(int argc, char *argv[])
{
	if(argc!=2)
	{
		printf("./mycat filename\n");
		return -1;
	}
	int fd = open(argv[1],O_RDONLY);
	//读，输出到屏幕

	char buf[256];
	int ret = read(fd,buf,sizeof(buf));
	while(ret)//循环输出，直到文件末尾
	{
		write(STDOUT_FILENO,buf,ret); 
		ret = read(fd,buf,sizeof(buf));
	}
	close(fd);
	return 0;
}
```
生成可执行文件`mycat`
```
seaworth@ubuntu:~/Desktop/io$ gcc mycat.c -o mycat
seaworth@ubuntu:~/Desktop/io$ ./mycat filename
```

## 打开一个文件，写入helloworld，并读取文件内容显示
创建`myreadwrite.c`文件
```
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>

int main(int argc, char *argv[])
{
	if(argc!=2)
	{
		printf("./myreadwirte filename\n");
		return -1;
	}
	//创建文件并写入hello world
	//读取文件中的内容，并显示
	int fd = open(argv[1],O_RDWR|O_CREAT,0666);
	char input[]="hello world";
	write(fd,input,sizeof(input));
	//这时候文件指针在文件的末尾，需要重新指定文件指针的位置到文件首
	lseek(fd, 0, SEEK_SET);
	char buf[256]={0};
	int ret = read(fd,buf,sizeof(buf));
	while(ret)
	{
		write(STDOUT_FILENO,buf,ret); //STDIN_FILENO,STDERR_FILENO
		ret = read(fd,buf,sizeof(buf));
	}
	close(fd);
	return 0;
}

```
编译程序，生成可执行文件`myreadwirte`
```
seaworth@ubuntu:~/Desktop/io$ gcc myreadwrite.c -o myreadwrite
seaworth@ubuntu:~/Desktop/io$ ./myreadwrite hello
hello worldseaworth@ubuntu:~/Desktop/io$ 
```















