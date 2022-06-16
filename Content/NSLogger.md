# NSLogger 调研

仓库地址：https://github.com/fpillet/NSLogger.git

这是一款比较轻量级的日志库，同时支持iOS和Android的接入，并配有mac版本的日志查看工具，可对关键日志进行筛选查看，并具有非常好的着色效果。但随着mac电脑的更新升级，库的更新有点力不从心。


主要有以下几个问题：

* 1、从APP中导出的日志，无法通过 NSLoggerViewer 在 M1 的mac电脑上打开，一打开就crash掉；

解决方案，下载代码自行编译一个版本。改动的地方包括：

1、移除了 HockeySDK.framework，这个是用来做crash采集的，这个很多年没更新了，不支持M1 arm64的架构；
2、LoggerAppDelegate.m 文件中删除了 HockeySDK 初始化的代码；
3、修改了 MACOSX_DEPLOYMENT_TARGET 版本号；
4、删除了 relaunch target；
5、自定义了默认的筛选配置，便于直接使用，如果用户在界面上修改了筛选条件，内容会被保存到 NSUserDefault 中。


* 2、我们在使用库 `LoggerSetViewerHost(LoggerGetDefaultLogger(), (__bridge CFStringRef)(IP), port);` 方法时，如果`NSLogger for mac`没有打开，则APP会出现类似于死循环的效果，手机的CPU瞬间拉满。

原因是，NSLogger认为目标 host 能够连通就任务可以建立 TCP连接，而此时我们的电脑并未打开 NSLoggerViewer；NSLogger就会不断地发送消息，不断地重连。解决方案：

1、在 `LoggerWriteStreamCallback`方法中，判断写操作出错的地方，及 `case kCFStreamEventEndEncountered: `下面 `sleep(1)`；减少CPU的占用。


从这个库中学到了一个宏，即 `__attribute__((used))`，used的作用是告诉编译器，我声明的这个符号是需要保留的。被used修饰以后，意味着即使函数没有被引用，在Release下也不会被优化。如果不加这个修饰，那么Release环境链接器会去掉没有被引用的段。这种情况一般被用在动态链接的时候，使用的场景不多。
