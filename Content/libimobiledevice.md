# libimobiledevice安装

```
brew install libimobiledevice
```

# libimobiledevice功能介绍

仓库地址：https://github.com/libimobiledevice/libimobiledevice

主要功能列表如下面的截图：

<img width="1205" alt="image" src="https://user-images.githubusercontent.com/6744261/164188901-3d371893-93b4-4414-9b99-9893b90426cc.png">

## 1. 查看已连接设备

```
idevice_id --list
```

## 2. 安装和卸载ipa

```
# 查看手机上安装的应用
ideviceinstaller -l

# 安装ipa包
ideviceinstaller -i xxx.ipa

# 卸载ipa包
ideviceinstaller -u com.company.productname
```

## 3. 查看系统日志

```
idevicesyslog
```

## 4. 截图

```
idevicescreenshot
```

## 5. 获取设备信息

```
# 批量获取设备信息
ideviceinfo

# 获取设备指定项的信息
ideviceinfo -k DeviceName

# 获取设备名称
idevicename
```

## 6. 获取设备时间

```
idevicedate
```

## 7. 设置代理

可以将iPhone的指定端口设置到USB连接的电脑上，越狱场景用的比较多。

```
# 将iPhone 22端口代理到电脑的2222端口
iproxy 2222 22

# 访问电脑的2222端口，就可以访问iPhone
ssh -p 2222 root@127.0.0.1
```

## 8. 查看和操作设备的provision信息

```
# 查看 
ideviceprovision ideviceprovision

# 其他操作可看help
ideviceprovision
```

## 9. 挂载程序

```
ideviceimagemounter
```

## 10. 调试程序

借助idevicedebug，smart_monkey可以实现iPhone的monkey测试。[monkey测试](https://testerhome.com/topics/2523)

```
# 启动某个应用
idevicedebug run com.company.productname
```

接下来怎么使用，还有深入了解[API 文档](https://docs.libimobiledevice.org/libimobiledevice/latest/files.html)


## 11. 更改iOS系统定位

```
# 重置
idevicesetlocation -u 94170a62c9c8657d989ba21a53c2e78c9b0f405d reset
# 更改
idevicesetlocation -u 94170a62c9c8657d989ba21a53c2e78c9b0f405d -- 39.932199 116.411168
```

## 12. 导出iOS系统中的crash日志

```
idevicecrashreport -k ./crash
```



参考文档：
https://cloud.tencent.com/developer/article/1521371
