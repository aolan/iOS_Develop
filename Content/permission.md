# iOS权限梳理

## iOS定位权限

iOS定位权限一直在不断优化，开发者不得不过段时间就去check一下最新的权限规则。我们从iOS14开始说起，因为iOS14之后权限有了变化。iOS对定位一共有以下几种权限：

```swift

public enum CLAuthorizationStatus : Int32, @unchecked Sendable {
    case notDetermined = 0
    case restricted = 1
    case denied = 2
    case authorizedAlways = 3
    case authorizedWhenInUse = 4
}
```

通过枚举很容易理解，那么这些权限该如何去申请呢？大部分我们的业务场景可分为【获取一次位置】、【后台一直使用位置】

### 获取一次位置

一般用于线上线下的业务场景，我们需要获取到用户的位置，然后根据位置推荐不同的内容。那么这种怎么申请权限呢？

* 1、Info.plist 文件中，添加使用位置描述，Key为`Privacy - Location When in Use Usage Description`

* 2、然后使用CoreLocation中提供的CLLocationManager去申请位置权限，代码如下

  ```swift

  class RequestPermission: NSObject {

      func requestPermission() {
          let mgr = CLLocationManager()
          mgr.delegate = self
          // 先判断一下去

      }
  }
  

  ```



