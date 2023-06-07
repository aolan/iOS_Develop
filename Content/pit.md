# 获取gateway的坑



# 无线数据权限的坑

从iOS10开始，国内增加了特供功能，在使用用户的WIFI和蜂窝网络时，需要申请相应的权限，然而该权限的申请并未提供给开发者。
开发者只能调用某个接口，来触发该权限弹框的弹出。因此引入了一个巨坑，该权限弹框并不是总能文档的弹出，导致随机的应用无法访问网络。遇到这种情况怎么办？

## 1 应用启动时检查蜂窝网络权限

```swift
    func syncRequestCellularPermission() -> Bool {
        
        var havePermission = false
        let semaphore = DispatchSemaphore(value: 0)
        let cellularData = CTCellularData()
        cellularData.cellularDataRestrictionDidUpdateNotifier = { state in
            havePermission = state == .notRestricted
            semaphore.signal()
        }
        semaphore.wait()
        cellularData.cellularDataRestrictionDidUpdateNotifier = nil
        // 没有权限，只是弹框引导用户去设置页面
        if !havePermission {
            DispatchQueue.main.async {
                // TODO: 弹窗提醒
            }
        }
        return havePermission
    }
```

## 2 坑来了

在某些情况下，应用检测出没有权限，弹框提醒用户去授权无线数据权限，等用户跳转到应用权限设置页面，却没有无线数据这一项。
![image](https://github.com/aolan/iOS_Develop/assets/6744261/b9b20342-d45f-4474-809b-7eb6fc3d2320)

从网上搜索的信息来看，应用开发者无法解决这个问题，只能通过提示用户该怎么操作，恢复出无线数据这一项。

* 方法一：【设置】-【蜂窝网络】-【无线局域网助理】打开，然后重启应用，我没有尝试过，不知道能不能行，因为我们还没遇到过这种情况

* 方法二：【设置】-【通用】-【传输或还原iPhone】-【还原】-【还原网络设置】，执行这步操作，所有用到无线数据的应用会重新申请无线数据权限，
但同时带来的副作用是，所用使用了无线数据的应用，都要重新授权【无线数据】权限。

       
        
