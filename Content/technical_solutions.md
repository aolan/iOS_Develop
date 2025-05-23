# 常见技术方案

## 1. 是否有某种方案可以读取苹果手机中的短信？

1. 过滤垃圾短信的方案（不可行）
    * 基于 IdentifyLookup 框架，开发 Message Filter Extension 来确认短信类别，但该应用程序扩展没有访问网络权限，也无法将数据写入应用的共享容器中。
    * 读取的短信有限制：无法获取联系人列表中的人发送的消息、无法获得 iMessage 中的信息、回复发件人 3 次及以上的会话也无法获得。
 
2. 通过 iCloud 获取（不可行）
    * 通过 iCloud 直接读取 iPhone 手机中的短信（SMS）或 iMessage 内容，在技术上是可行的，但存在显著的限制和挑战。苹果对用户隐私保护非常严格，因此并未公开提供直接访问这些数据的 API



## 2. 是否有某种方案，可以在其他设备接听/挂断苹果手机的电话？

有。蓝牙协议支持

* 苹果设备通常使用标准的蓝牙协议（如 HFP/HSP，即免提配置文件/耳机配置文件）进行电话功能的扩展。
* 自研设备需要支持这些蓝牙协议，尤其是 HFP（Hands-Free Profile），以实现电话接听和语音传输功能。


## 3. 第三方应用是否可以使用苹果的NFC功能?

是的，自研应用可以基于苹果的Core NFC框架来读取NFC标签。Core NFC 是苹果提供给开发者的一套框架，允许应用程序读取NFC标签中的数据。通过使用这个框架，你可以为你的应用添加NFC功能，比如扫描NFC标签以获取信息或执行特定操作。需要iPhone 7或更新机型，并且运行iOS 11或更高版本。对于NFC写入和某些高级特性，则可能需要更新的iOS版本和设备。

## 4. iPhone手机是否可以被远程控制？

暂时没找到可以控制的方法。

1、iPhone可以利用 ReplyKit 进行直播投屏；

2、但被投屏的设备控制 iPhone，尚未找到解决方案。

待实践：
https://www.bilibili.com/video/BV1hU4y1v7aY/?spm_id_from=333.337.search-card.all.click&vd_source=96b68abf2139cda0ff8006143e8f76cc（苹果系统自带的远程控制）
https://www.bilibili.com/video/BV1Xp4y1P7j1/?spm_id_from=333.337.search-card.all.click&vd_source=96b68abf2139cda0ff8006143e8f76cc（看上去像是用电脑模拟鼠标）




参考：

https://juejin.cn/post/7237697495109845052

https://www.jianshu.com/p/44eb077d58e7

https://www.jianshu.com/p/e30d874d450f


