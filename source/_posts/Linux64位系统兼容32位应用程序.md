---
title: Linux64位系统兼容32位应用程序
date: 2018-09-18 17:21:40
tags: [linux,驱动,兼容]
categories: [Linux驱动]
---

# 1. 问题现象

在进行64位驱动兼容32位应用程序的修改中，发现了如下几个问题：

> 1. 32位应用程序调用ioctl时，返回ENOTTY（inappropriate ioctl for device）错误。经过调试发现根本没调用到内核的ioctl函数。
> 2. 解决完第一个问题后，应用程序进入了ioctl中，但是用户程序和驱动打印的ioctl命令号不一样，导致在switch的时候，找不到对应命令号。
> 3. 从用户空间拷贝数据的时候（copy_from_user()）失败。拷贝的数据结构里面含有指针。

# 2. 问题分析

## 2.1 第一个问题

在 `Linux kernel 2.6.36` 中已经完全删除了 `struct file_operations` 结构中的 `ioctl` 函数指针，取而代之的是 `unlocked_ioctl`。之后的内核版本中，`struct file_operations` 含有下面两个函数指针：
<!-- more -->
``` C
struct file_operations {
        ... ...
    long (*unlocked_ioctl) (struct file *, unsigned int, unsigned long);
    long (*compat_ioctl) (struct file *, unsigned int, unsigned long);
        ... ...
}；
```

*  如果是64位用户程序运行在64位的内核上，调用的是unlocked_ioctl;

   如果是32位用户程序运行在32位的内核上，调用的也是unlocked_ioctl;

*  支持64位的驱动必须要实现compat_ioctl，如果没有实现，那么32位的用户程序在64位的内核上执行ioctl时会返回错误 ENOTTY。

要解决第一个问题，就需要在驱动层实现 `compat_ioctl`.

## 2.2 第二个问题

命令号中的参数部分含有指针，32位用户程序的指针是4字节，64位驱动的指针是8字节，导致最终计算出的命令号不一致。可采用如下方法解决：
*  尽量避免使用指针类型
*  提供64位和32位大小一致的结构
*  先将传入的指针转成64位，使其能进入ioctl函数，在函数内部再对指针进行处理。

## 2.3 第三个问题

由于传入内核的数据结构里面含有指针类型，所以在拷贝的时候，用户态结构和内核态结构的大小就不一样，导致拷贝失败。解决方法如下：

* 使用compat_ptr()宏转换64位的unsigned long数据类型到32位的地址。
* 对ioctl函数的参数也要使用compat_ptr()进行转换。
* 避免使用指针，可用 `char[0]` 或 `char[1]` 代替。关于`char[0]` 和 `char[1]`的用法，参照[这篇文章](https://do-do-do.github.io/C%E8%AF%AD%E8%A8%80%E7%BB%93%E6%9E%84%E4%BD%93%E4%B8%ADchar-0-%E5%92%8Cchar-1-%E7%9A%84%E7%94%A8%E6%B3%95.html)。



---