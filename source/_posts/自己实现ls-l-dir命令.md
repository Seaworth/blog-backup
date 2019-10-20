---
title: 自己实现ls -l dir命令
date: 2019-10-20 11:25:50
toc: true
tags:
	- Linux
---
{% blockquote 【美】彼得·德鲁克, 《卓有成效的管理者》 %}
时间永远是最短缺的，没有任何东西可以替代已失去的时间。有效的管理者与他人最大的区别，就是他们非常珍惜自己的时间。但是往往人最不擅长管理自己的时间。
{% endblockquote %}
<!-- more -->

{% blockquote %}
本次要实现的功能是ls -l dir，就是罗列出dir文件夹下的所有文件的详细信息，要实现这个功能需要学习stat、opendir、readdir、closedir函数。stat函数主要用来获取每个文件或文件夹的相关属性，dir相关的三个函数主要用来获取文件夹下的所有文件，然后再对每个文件获取相关的属性，实现的功能如下图所示。
{% endblockquote %}

{% asset_img Snipaste_2019-10-20_15-01-35.png This is an image %}

# 1.相关函数
## 1.1 stat函数
在Linux终端查看stat函数的说明，使用`man 2 stat`。stat函数需要包含的头文件和函数声明如下。
```
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>

int stat(const char *pathname, struct stat *buf);
```
功能：调用stat，得到文件相关的属性
描述：
pathname，文件或路径的名称
buf，stat结构体的指针
{% codeblock a stat structure %}
struct stat {
   dev_t     st_dev;         /* ID of device containing file */
   ino_t     st_ino;         /* inode number */
   mode_t    st_mode;        /* protection */
   nlink_t   st_nlink;       /* number of hard links */
   uid_t     st_uid;         /* user ID of owner */
   gid_t     st_gid;         /* group ID of owner */
   dev_t     st_rdev;        /* device ID (if special file) */
   off_t     st_size;        /* total size, in bytes */
   blksize_t st_blksize;     /* blocksize for filesystem I/O */
   blkcnt_t  st_blocks;      /* number of 512B blocks allocated */

   /* Since Linux 2.6, the kernel supports nanosecond
	  precision for the following timestamp fields.
	  For the details before Linux 2.6, see NOTES. */

   struct timespec st_atim;  /* time of last access */
   struct timespec st_mtim;  /* time of last modification */
   struct timespec st_ctim;  /* time of last status change */

#define st_atime st_atim.tv_sec      /* Backward compatibility */
#define st_mtime st_mtim.tv_sec
#define st_ctime st_ctim.tv_sec
};
{% endcodeblock %}

## 1.2 opendir函数
在Linux终端查看opendir函数的说明，使用`man opendir`。
```
#include <sys/types.h>
#include <dirent.h>

DIR *opendir(const char *name);
```
功能：打开一个文件夹。
描述：
name，文件的名字
返回值是一个指向文件流的指针。

## 1.3 readdir函数
在Linux终端查看readdir函数的说明，使用`man readdir`。
```
#include <dirent.h>

struct dirent *readdir(DIR *dirp);
```
功能：读文件夹
描述：
dirp，一个指向文件流的指针，和opendir函数的返回值是同一个类型。
dirent，dirent结构体的指针
{% codeblock dirent structure %}
struct dirent {
   ino_t          d_ino;       /* inode number */
   off_t          d_off;       /* not an offset; see NOTES */
   unsigned short d_reclen;    /* length of this record */
   unsigned char  d_type;      /* type of file; not supported
								  by all filesystem types */
   char           d_name[256]; /* filename */
};
{% endcodeblock %}

## 1.4 closedir函数
在Linux终端查看closedir函数的说明，使用`man closedir`。
```
#include <sys/types.h>
#include <dirent.h>

int closedir(DIR *dirp);
```
功能：关闭文件夹。
描述：
dirp，文件流的指针。

# 2.功能实现
要实现ls -l dir的功能，就要实现两个函数，一个是DirCount函数，遍历当前目录下的所有文件。第二是GetFileInfo函数，利用stat函数，取得传入该函数的文件的属性，并连接成字符串，打印出来。

{% codeblock 13_homework.c %}
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>
#include <string.h>
#include <unistd.h>
#include <time.h>
#include <grp.h>
#include <pwd.h>
#include <dirent.h>

void GetFileInfo(char* filename);
//目录计数
int DirCount(char *dirname)
{
	//	printf("%s\n", dirname);
	//打开目录
	DIR *dirp = opendir(dirname);
	if(dirp == NULL)
	{
		perror("opendir err");
		return -1;
	}

	//循环读目录，如果是普通文件，count++，如果是目录，继续调用DirCount
	struct dirent *dentp = NULL;
	while( (dentp = readdir(dirp)) !=NULL ) 
	{

		if(strcmp(".", dentp->d_name)==0 || strcmp("..", dentp->d_name)==0)
		{
			continue;//如果是当前目录和上一级目录，跳过
		}

		GetFileInfo(dentp->d_name);
	}
	//关闭目录
	closedir(dirp);
	return 0;
}
void GetFileInfo(char* filename)
{
	//调用stat，得到文件属性信息
	struct stat sb;
	stat(filename,&sb);
	char stmode[11]={0};

	memset(stmode,'-',sizeof(stmode)-1);

	if(S_ISREG(sb.st_mode)) stmode[0]='-';
	if(S_ISDIR(sb.st_mode)) stmode[0]='d';
	if(S_ISCHR(sb.st_mode)) stmode[0]='c';
	if(S_ISBLK(sb.st_mode)) stmode[0]='b';
	if(S_ISFIFO(sb.st_mode)) stmode[0]='p';
	if(S_ISLNK(sb.st_mode)) stmode[0]='l';
	if(S_ISSOCK(sb.st_mode)) stmode[0]='s';

	//解析权限


	if(sb.st_mode & S_IRUSR)
	{
#if 0
		printf("%d\n",S_IRUSR);
		printf("%d\n",sb.st_mode);
		printf("%d\n",(sb.st_mode&S_IRUSR));
		printf("%x\n",0b101&0b101);
#endif
		stmode[1]='r';
	}
	if(sb.st_mode & S_IWUSR) stmode[2]='w';
	if(sb.st_mode & S_IXUSR) stmode[3]='x';

	if(sb.st_mode & S_IRGRP) stmode[4]='r';
	if(sb.st_mode & S_IWGRP) stmode[5]='w';
	if(sb.st_mode & S_IXGRP) stmode[6]='x';

	if(sb.st_mode & S_IROTH) stmode[7]='r';
	if(sb.st_mode & S_IWOTH) stmode[8]='w';
	if(sb.st_mode & S_IXOTH) stmode[9]='x';


	//测试memset的用法
#if 0
	char str[] = "almost every programmer should know memset!";
	puts (str);
	memset (str,'-',6);
	puts (str);	
#endif

	struct tm *filetm = localtime(&sb.st_atim.tv_sec);
	char* str;
	str=ctime(&sb.st_mtime);
	char timebuf[13] ={0};
	strncpy(timebuf,str+4,12);
	timebuf[13] = '\0';//要添加结束符，不然打印会出现乱码
	//puts(timebuf);

	printf("%s %ld %s %s %6ld %12s %s\n",stmode,sb.st_nlink,getpwuid(sb.st_uid)->pw_name,getgrgid(sb.st_gid)->gr_name,sb.st_size,timebuf,filename);
}

int main(int argc, char*argv [])
{
	if(argc !=2 )
	{
		printf("./13_homework filename\n");
		return -1;
	}
	DirCount(argv[1]);
	return 0;
}
{% endcodeblock %}

相关的Makefile文件。
{% codeblock makefile %}
srcFiles := $(wildcard *.c)
objects := $(patsubst %.c,%,$(srcFiles))

all:$(objects)
.PHONY:all

%:%.c
	gcc $^ -o $@ -g

.PHONY:clean
clean:
	-rm -f $(objects)
{% endcodeblock %}
然后再终端执行`make`，生成执行文件。
```
seaworth@ubuntu:~/Desktop/file_dir$ make
gcc 13_homework.c -o 13_homework -g
seaworth@ubuntu:~/Desktop/file_dir$ ./13_homework ./
-rw-rw-r-- 2 seaworth seaworth    370 Oct 12 10:46 hello.txt
-rwxrwxr-x 1 seaworth seaworth  11304 Oct 20 16:28 02_ls
-rw-rw-r-- 1 seaworth seaworth   1861 Oct 15 19:10 02_ls.c
-rwxrwxr-x 1 seaworth seaworth  10112 Oct 20 16:28 01_openmax
-rw-rw-r-- 1 seaworth seaworth    246 Oct 12 11:05 06_readlink_unlink.c
-rw-rw-r-- 1 seaworth seaworth    137 Oct 12 15:38 10_mkdir.c
-rwxrwxr-x 1 seaworth seaworth   9752 Oct 20 16:28 10_mkdir
-rw-rw-r-- 1 seaworth seaworth    237 Sep 29 17:04 stat.c
```

