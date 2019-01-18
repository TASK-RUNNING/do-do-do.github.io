---
title: C语言结构体中char[0]和char[1]的用法
id: a-2018-09-18-17-45-22
date: 2018-09-18 17:45:22
tags: [C语言,结构体,指针,内存]
categories: [编程语言]
keywords: [C语言,结构体,指针,内存]
description: C语言结构体中char[0]和char[1]的用法
---

<script type="text/javascript" src="/js/src/baidu.js"></script>

# 1.写在前面

我在进行Linux 64位驱动程序兼容32位应用程序的适配过程中，深深的感觉指针操作带来的麻烦，特别是应用层的32位指针传到内核层后，指针大小变成64位，需要进行频繁的大小调整，及其难受。等我快完成所有工作的时候，听一位同事说可以使用char[0]用法来代替指针，我差点一口老血喷出来。“你咋不早说...”。接下来从网上各种google，发现了这种用法的巧妙，特写下此篇文章，以做记录。
（PS：还是要感谢我那位同事YYL，让我又get到一个技能^_^）

在结构体最后加char[0]或char[1]的用法是GNU C的扩展，<font color=#FF0000>在ISO/IEC 9899-1999里面，这么写是非法的</font>。这种用法在C99中叫做 **柔性数组**。柔性数组成员前面必须至少有一个其它类型成员。包含柔性数组成员的结构要用`malloc`进行动态内存分配，并且分配的内存应该大于结构的大小，以适应柔性数组的预期大小。

<!-- more -->

# 2.引用这种用法的目的

> 主要是为了方便管理内存缓冲区，如果你直接使用指针而不使用数组，那么，你在分配内存缓冲区时，就必须分配结构体一次，然后再分配结构体内的指针一次，（而此时分配的内存已经与结构体的内存不连续了，所以要分别管理即申请和释放）而如果使用数组，那么只需要一次就可以全部分配出来，反过来，释放时也是一样，使用数组，一次释放，使用指针，得先释放结构体内的指针，再释放结构体。还不能颠倒次序。

其实就是<font color=#FF0000>分配一段连续的的内存，减少内存的碎片化</font>。

# 3.用法

``` C
struct Msg
{
    ...         // 其它成员
    ...         // 其它成员
    int nLen;   // 一般char data[0]的前面会加一个长度nLen表示data的大小
    char data[0];   // char[0]或char[1]必须放在最后
};
```

我们要知道的一点就是：**char data[0] 这个数组是没有元素的，它的地址紧跟着nLen后的地址，如果分配的内存大于结构体的实际大小，那么大出来的那部分就是data的内容。**

实际使用时，一般这样用

``` C
int dataBytes = 10;             // 此处指定data的数据大小
struct Msg *p = (struct Msg *)malloc(sizeof(struct Msg) + dataBytes);   // 动态分配
p->nLen       = dataBytes;      // 把长度赋值给nLen，以方便其它部分使用此结构体
```

如果还不明白，撸一串代码，一看便知：

```C
// test.c
#include <stdio.h>
#include <string.h>

typedef struct body1
{
    int a;
    int b;
}__attribute ((packed)) BODY1;  //__attribute ((packed))是强制不进行字节对齐

typedef struct body2
{
    int len;
    char *data;
}__attribute ((packed)) BODY2;

typedef struct body3
{
    int  len;
    char data[0];
}__attribute ((packed)) BODY3;

typedef struct body4
{
    int  len;
    char data[1];
}__attribute ((packed)) BODY4;

int main()
{
    BODY1 b1;
    BODY2 b2;
    BODY3 b3;
    BODY4 b4;

    memset(&b1, 0, sizeof(BODY1));
    memset(&b2, 0, sizeof(BODY2));
    memset(&b3, 0, sizeof(BODY3));
    memset(&b4, 0, sizeof(BODY4));

    printf("sizeof(b1)   = %ld\n", sizeof(b1));
    printf("sizeof(b2)   = %ld\n", sizeof(b2));
    printf("sizeof(b3)   = %ld\n", sizeof(b3));
    printf("sizeof(b4)   = %ld\n", sizeof(b4));

    printf("     b2 addr = %p\n", &b2);
    printf("b2.data addr = %p\n", b2.data);
    printf("     b3 addr = %p\n", &b3);
    printf("b3.data addr = %p\n", b3.data);
    printf("     b4 addr = %p\n", &b4);
    printf("b4.data addr = %p\n", b4.data);

    return 0;
}
```

程序是在64位系统下编译，运行结果如下:

```C
sizeof(b1)   = 8
sizeof(b2)   = 12
sizeof(b3)   = 4
sizeof(b4)   = 5
     b2 addr = 0x7ffded4f3633
b2.data addr = (nil)
     b3 addr = 0x7ffded4f363f
b3.data addr = 0x7ffded4f3643
     b4 addr = 0x7ffded4f3643
b4.data addr = 0x7ffded4f3647
```

从上面的结果可以看出：

* char data[0]是不占用任何空间的，而char *data占用了一个指针变量的大小，千万不要把char data[0]当做一个指针，它其实是一个偏移量，这个偏移量指向结构体后紧挨着的空间。

* char[1]是占用空间的，如果没加强制不进行字节对齐，则结构体的大小会是8。char[0]和char[1]的作用是相同的。

* b3的data地址，是b3结构体开始的地址加上len所占用的4字节的地址，b4也是一样。

# 4.用指针和char[0]的区别

1. 结构体中使用指针：创建时，系统先为结构体分配内存，再分配指针指向的data的内存。两块内存不连续。释放的时候，先释放指针指向的内存，再释放结构体内存。
2. 结构体中使用char[0]：创建时，系统一起为其分配结构体的内存和data的内存，两块内存是连续的（更确切的说是一块内存）。释放的时候，一次性释放。



---

