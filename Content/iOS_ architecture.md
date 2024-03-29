# iOS 架构

## iOS与MacOS的不同点

* iOS是基于ARM架构，而 OS X是基于 Intel i386 和 x86_64；
* iOS的内核是闭源的（苹果2017年开源了iOS的XNU内核代码），OS X内核是开源的；
* iOS不允许访问Darwin，没有root访问权限，只能访问自己目录内的数据。

## iOS架构图

![image](https://user-images.githubusercontent.com/6744261/154897930-8baefd93-47f5-45fb-aca9-e37f19153a41.png)

* UI层： 主要有SpringBoard、Spotlight等UI交互界面
* 应用框架层：主要有 Cocoa Touch
* 核心框架层：主要有 OpenGL、Quartz等图形、多媒体组件
* Darwin：操作系统核心，包括XNU内核和UNIX shell

## Darwin架构

![image](https://user-images.githubusercontent.com/6744261/154898166-1f2d6395-41d3-4461-ba9d-47517750ea91.png)

Darwin的内核是XNU，XNU is Not Unix。XNU是两种技术的混合体，Mach和BSD。BSD层确保了Darwin系统的UNIX特性，真正的内核是Mach，但是对外部隐藏。BSD以上属于用户态，所有的内容都可以被应用程序访问，而应用程序不能访问内核态。当需要从用户态切换到内核态的时候，需要通过mach trap实现切换。

## XNU包括

* Mach微内核
* BSD层
* libkern
* I/O Kit

## Mach

Mach 是 XNU的原子核，是一个微内核轻量级操作系统，仅处理最核心的任务

* 进程和线程的抽象
* 任务调度
* 进程间通讯和消息传递
* 虚拟内存管理

## BSD

BSD层简历在Mach之上，确保了Darwin符合 POSIX 。提供了更高层次的功能，包括：

* UNIX 进程模型
* POSIX线程模型（Pthread）及相关的同步原语
* UNIX 用户和组
* 网络协议栈（BSD Socket API）
* 文件系统访问
* 设备访问（通过/dev目录访问）

## libKern

libKern 是C++ 编写的；为了支持C++运行时并提供所需要的基类，是一个內建的自包含的C++库。

I/O Kit
这是一个在内核中的完整的自包含的执行环境，让开发者可以使用C++快速创建设备驱动程序。


## POSIX系统调用
POSIX 兼容性是由XNU中BSD层提供的。
所有的POSIX 系统调用不论底层实现如何都有相同的原型，也就是说具有相同的参数和返回值。
系统调用编号：除了固定的原型之外，POSIX还完整定义了系统调用的编号。

## Mach系统调用
BSD层是对Mach内核的包装，但是Mach系统调用仍然可以在用户态访问。需要借助mach trap实现用户态到内核态的转换。
在32位系统上，Mach系统调用的编号都为负数，POSIX调用编号为非负。
在64位系统上，Mach系统调用为正数，但是以0x2000000开头，而POSIX调用编号以0x1000000开头。








参考： https://www.jianshu.com/p/029cc1b039d6
