
# iPhone开发外设方案调研

[参考链接](http://www.jianshu.com/p/852bf92c5c92)

![方案](http://upload-images.jianshu.io/upload_images/2128042-da2454281deacd20.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 网络端口

建立 `Socket` 使用 `TCP/IP` 协议族进行通信，天然支持多通道，想要几个通道就建几个 `Socket` 就可以，它主要有三种方式：

|   连接方式  | 硬件要求  |  优点  |  缺点 | 
| -------------------- | -------------------- | -------------------- | -------------------- |
| WIFI连接 | 外设要有网卡 | 不需要MFi芯片 | 无线连接信号不稳定 |
| USB热点共享| USB线，不确定是否需要网卡 | 有线稳定 | 这种应该只是做流量共享，APP和外设直接应该不能互相传输数据 | 
| NCM | USB线、MFi芯片 | 有线稳定 | MFi认证门槛 | 


### EAP (External Accessory)

EAP 全拼是 External Accessory Protocol，即外部设备协议。这个是苹果推荐使用的外设连接方式。需要外设集成 MFi 芯片进行 MFi 认证。手机端开发相对简单，只要集成 iOS 系统提供的一个框架 ExternalAccessory.framework，并且在 info.plist 中配置好协议字符串 (Supported external accessory protocols)，当 iOS 设备通过 USB 或者蓝牙连接到对应硬件时，iOS 系统会把符合 MFi 认证要求的外设抽象成了一个流对象，App 通过指定的协议字符串来创建一个 EASession 类的实例来访问到该流对象，就能通过 NSInputStream 和 NSOutputStream 跟硬件件进行通信了。

| 模式 | 优点 | 缺点 | 
| -------------------- | -------------------- | -------------------- |
| EASession 模式 |  允许同时通过多个协议字符串创建多个会话，也就是说直接支持多通道 | 带宽较低 | 
| Native Transport 模式 | 带宽足够大，理论值是 100MB 以上 | 不支持多通道 |


### BLE (iOS7.0 之后)

| 优点  | 缺点 | 
| -------------------- | -------------------- |
| 不需要集成 MFi 芯片做认证，功耗低 | 带宽很低，一般适合于只需要传输少量数据的场景 | 


### USBMuxd

USBMuxd，利用这种连接方式不需要做 MFi 认证，支持 iPhone 上的 App 跟外设通过进行通信，非常方便。

iPhone 的 iOS 系统中自带了 USBMuxd 服务，该服务能够实现
USB-TCP 协议的转换，能够把 USB 的端口映射到本机 (localhost) 的 TCP 端 (基于 Unix Domain Socket )。只需要在外设端也实现一个 USBMuxd 服务，并指定端口映射关系， 那
iPhone 的 App 和外设上的应用就可以使用 Socket 进行 TCP 进行通信了。

![原理](http://upload-images.jianshu.io/upload_images/2128042-d5394a92be2a1453.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开源项目 [peertalk](https://github.com/rsms/peertalk) 就是一个完整的使用 USBMuxd 方式实现 iPhone App 跟 Mac App 进行 TCP 通信的例子。


### MFI认证查询地址 https://mfi.apple.com/MFiWeb/getAPS.action

查询UPC/EAN码，目前中国有700多家公司的硬件设备完成了MFI认证。

