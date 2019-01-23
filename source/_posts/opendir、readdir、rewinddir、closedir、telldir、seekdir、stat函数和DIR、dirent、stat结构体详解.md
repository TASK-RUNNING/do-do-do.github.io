---
title: opendir、readdir、rewinddir、closedir、telldir、seekdir、stat函数和DIR、dirent、stat结构体详解
id: a-20190123-095755
tags: 
  - dir
  - 结构体
categories:
  - Linux
  - 编程语言
comments: true
copyright: true
date: 2019-01-23 09:57:55
keywords: 
  - opendir
  - readdir
  - rewinddir
  - closedir
  - telldir
  - seekdir
  - stat
  - DIR
  - dirent
  - stat
description: opendir、readdir、rewinddir、closedir、telldir、seekdir、stat函数和DIR、dirent、stat结构体详解
---

<script type="text/javascript" src="/js/src/baidu.js"></script>

# 1. opendir()

``` c
// 头文件
#include<sys/types.h>
#include<dirent.h>

// 函数原型
DIR* opendir (constchar * path );

// 功能：打开一个目录
// 返回值：成功则返回DIR*型态的目录流, 打开失败则返回NULL.

// DIR结构体：
struct __dirstream
{
    void    *__fd;
    char    *__data;
    int      __entry_data;
    char    *__ptr;
    int      __entry_ptr;
    size_t   __allocation;
    size_t   __size;
    __libc_lock_define (, __lock)
};
typedef struct __dirstream DIR;

// 此结构体一般被以下函数使用：
struct dirent *readdir(DIR *dp);
void rewinddir(DIR *dp);
int closedir(DIR *dp);
long telldir(DIR *dp);
void seekdir(DIR *dp,long loc);
```

<!-- more -->

# 2. readdir()

```c
// 头文件
#include<sys/types.h>
#include<dirent.h>

// 函数原型
struct dirent* readdir(DIR* dp);     /* 需要循环读取dp中的文件和目录，每读取一个文件或目录都返回一个dirent结构体指针 */

// 功能：读取目录
// 返回值：dirent结构体指针

// dirent结构体
struct dirent
{
    long d_ino;                        /* inode number 索引节点号 */
    off_t d_off;                       /* offset to this dirent 在目录文件中的偏移 */
    unsigned short d_reclen; /* length of this d_name 文件名长 */
    unsigned char d_type;     /* the type of d_name 文件类型 */
    char d_name [NAME_MAX+1]; /* file name (null-terminated) 文件名，最长255字符 */
}
```

# 3. rewinddir()

```c
// 头文件
#include <sys/types.h>
#include <dirent.h>

// 函数原型
void rewinddir(DIR *dp);

// 功能：用来设置参数dp目录流目前的读取位置为原来开头的读取位置
```

# 4. closedir()

```c
// 头文件
#include <sys/types.h>
#include <dirent.h>

// 函数原型
int closedir(DIR*dp);

// 功能：关闭参数dp所指的目录流
// 返回值：关闭成功则返回0,，失败返回-1
```

# 5. telldir()

```c
// 头文件
#include <sys/types.h>
#include <dirent.h>

// 函数原型
long inttelldir(DIR *dp);

// 功能：获取当前dp位置
// 返回值：返回目录流dp的当前位置，此返回值代表距离目录文件开头的偏移量，有错误发生时返回-1
```

# 6. seekdir()

```c
// 头文件
#include <sys/types.h>
#include <dirent.h>

// 函数原型
void seekdir(DIR *dp,long int loc);

// 功能：用来设置参数dp目录流当前的读取位置，在调用readdir()时便从此新位置开始读取。参数loc代表距离目录文件开头的偏移量。
```

# 7. stat()

```c
// 头文件
#include <sys/stat.h>
#include <unistd.h>

// 函数原型
int stat(const char *file_name,struct stat *buf);

// 功能：通过文件名file_name获取文件信息，并保存在buf所指的结构体stat中
// 返回值：执行成功则返回0，失败返回-1

// stat结构体：
struct stat {
        mode_t     st_mode;       //文件访问权限
        ino_t      st_ino;       //索引节点号
        dev_t      st_dev;        //文件使用的设备号
        dev_t      st_rdev;       //设备文件的设备号
        nlink_t    st_nlink;      //文件的硬连接数
        uid_t      st_uid;        //所有者用户识别号
        gid_t      st_gid;        //组识别号
        off_t      st_size;       //以字节为单位的文件容量
        time_t     st_atime;      //最后一次访问该文件的时间
        time_t     st_mtime;      //最后一次修改该文件的时间
        time_t     st_ctime;      //最后一次改变该文件状态的时间
        blksize_t st_blksize;    //包含该文件的磁盘块的大小
        blkcnt_t   st_blocks;     //该文件所占的磁盘块
};
```

--------------------- 
