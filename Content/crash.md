## 程序发生crash时，会有一些信号量发出来


### SIGABRT

1. 多次free

> 对同一个指针`free()`2次可能会产生SIGABRT

2. 执行abort函数

3. 执行assert函数。

> 这个遇到过，在保存照片到相册是，发生crash，实际上没有申请保存相册的权限导致的，应该是苹果调用了断言。


### SIGBUS

意味着指针所对应的地址是有效地址，但总线不能正常使用该指针。通常是未对齐的数据访问所致。

1. 硬件故障。

2. Linux平台上执行malloc()，如果没有足够的RAM，Linux不是让malloc()失败返回，而是向当前进程分发SIGBUS信号。

3. 某些架构上访问数据时有对齐的要求，比如只能从4字节边界上读取一个4字节的数据类型。IA-32架构没有硬性要求对齐，尽管未对齐的访问降低执行效率。另外一些架构，比如SPARC、m68k，要求对齐访问，否则向当前进程分发SIGBUS信号。


### SIGSEGV

所谓的段错误就是指访问的内存超过了系统所给这个程序的内存空间。

1. buffer overflow --- usually caused by a pointer reference out of range. 

2. stack overflow --- please keep in mind that the default stack size is 8192K. 

3. illegal file access --- file operations are forbidden on our judge system.

#### 举例

```c

// 错误的访问类型会引起段错误

#include<stdio.h>
#include<stdlib.h>

int main()
{
    char *c = "hello world";
    c[1] = 'H';
}

```


```c

// 访问了不属于进程地址空间的内存

#include <stdio.h> 
#include <stdlib.h>

int main()
{ 
	int* p = (int*)0xC0000fff; 
    *p = 10; 
}　

```


```c

// 访问了不存在的内存

int *p = null;
*p = 1;

```


```c

// 内存越界，数组越界，变量类型不一致等

#include <stdio.h>

int main()
{ 
    char test[1]; 
    printf("%c", test[10]); 
    return 0; 
}　

```

```

// 试图把一个整数按照字符串的方式输出

int  main() 
{ 
    int b = 10; 
    printf("%s\n", b);
    return 0; 
}　

```



参考链接：

https://blog.csdn.net/codejoker/article/details/4543136