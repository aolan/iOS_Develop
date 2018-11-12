
# 《深入解析Mac OS X & iOS操作系统》学习笔记一

## Mac OS介绍

* NeXTSTEP采用的是Mach微内核系统，该内核是卡内基梅隆大学（CMU）开发的微内核。

* Mac OS X的第一个版本开始，内核就是Darwin（达尔文）。

* Mac OS X的核心组件包括 Cocoa、Mach、IOKit、XCode的Interface Builder，以及很多其他的组件，都是直接来自于NeXTSTEP。

* Darwin是操作系统的类UNIX核心，由kernel、XNU和运行时组成。

## Mac OS X版本代号

 * Mac OS X 10.0，Cheetah(非洲猎豹)

 * Mac OS X 10.1，Puma(美洲狮)

 * Mac OS X 10.2，Jaguar(美洲虎)

 * Mac OS X 10.3，Panther（美洲豹）

 * Mac OS X 10.4，Tiger（老虎）

 * Mac OS X 10.5，Leopard（美洲豹）

 * Mac OS X 10.6，Snow Leopard（雪豹）

 * Mac OS X 10.7，Lion（狮子）

 * Mac OS X 10.8，Mountain Lion（山狮）


 ## 系统分层

 * 用户体验层：Auqa、Dashboard、SpotLight和辅助功能。在iOS中，用户体验层包括SpringBoard、SpotLight。

 * 应用框架层：Cocoa、Carbon、Java。iOS只有Cocoa的衍生品Cocoa Touch。

 * 核心框架层：图形和媒体层，包括核心框架、Open GL、Quick Time。

 * Darwin：操作系统核心层（完全开源）


## UNIX的系统目录

OX X是一个符合UNIX标准的系统，因此也有那些标准UNIX具有的结构目录，UNIX目录在Finder中是看不见的。

 * /bin: UNIX中的二进制程序。这是常用UNIX命令所在的地方。（ls、mv、rm、df等）

 * /sbin: 系统程序。这些二进制程序用于系统管理，例如文件系统管理和网络配置等。

 * /usr: User目录。这并不是说这个目录是给用户用的，二更像是Windows中的Program Files目录，第三方软件可以安装在这里。

 * /usr: 目录中包含的bin、sbin、lib。/usr/lib用于存放共享的目标文件，这个目录还包含了一个include/子目录，所有标准的C头文件都存放在此。 
 
 * /etc: 其他文件。包含了大部分系统配置文件。如/etc/passwd，在OS X中，这个目录实际上是指向/private/etc的符号链接。

 * /dev: BSD设备文件。这些特殊文件表示了系统中存在的设备（字符设备和块设备）。

 * /tmp: 临时目录。是系统是中唯一所有人都可写的目录。在OS X中，这个目录实际上是指向/private/tmp的符号链接。

 * /var: 各种杂项文件。这个目录中保存了日志文件、邮件存储、打印队列和其他数据。在OS X中，这个目录实际指向/private/var的符号链接。


## OS X特有的目录

 * /Applications:  系统中所有应用程序的默认目录。

 * /Library: 系统应用的数据文件、帮助和文档等数据都在这个目录下。

 * /Network: 用于邻居节点发现和访问的虚拟目录。

 * /System: 系统文件目录。其中包含一个Library子目录，这个目录几乎包含了系统中所有重要组件。例如框架、内核、字体等。

 * /Users: 所有用户的主目录。每一个用户都会在这里创建一个自己的目录。

 * /Volumes: 可移动媒体和网络文件系统的挂载点所在目录。

 * /Cores: 如果启动了核心转储(core dump)，那么这个目录保存核心转储文件。


## 顶层框架

 Carbon和Cocoa是OS X中两个最重要的框架。

 * Carbon是OS 9遗留编程接口的名称。虽然Carbon被宣布已经废弃，不过还有很多应用程序依赖Carbon，甚至包括苹果自己的应用程序。

 * Cocoa是首选则应用程序编程环境。该框架本身并没有实现功能，而是基于其他框架构建的。如APPKit、CoreData、Foundation。封装了其他框架的框架通常称作保护伞(umbrella)框架。

 page 66
