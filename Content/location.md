# iOS定位

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

通过枚举很容易理解，那么我们该如何去获取位置信息呢？大部分我们的业务场景可分为【获取一次位置】、【后台一直使用位置】

我们先声明一个统一的返回结果：

```swift

import Foundation
import CoreLocation

/// 定位结果枚举
public enum LocationResult: String {
    /// 定位成功
    case success = "locationSuccess"
    /// 系统定位为开启
    case serviceDisable = "locationServiceDisable"
    /// 应用没有定位权限
    case permissionDenied = "locationPermissionDenied"
    /// 定位失败
    case failed = "locationFailed"
    /// 取消定位
    case cancel = "locationCanceled"
    
    /// 返回国际化文案
    public func toLocalizedString() -> String { Bundle.module.localizedString(forKey: self.rawValue, value: "", table: nil) }
}


/// 定位结果回调
public typealias LocationResultClosure = (_ result: LocationResult, _ location: CLLocation?) -> Void

```

## 1. 前台获取一次位置

一般用于线上线下的业务场景，我们需要获取到用户的位置，然后根据位置推荐不同的内容。那么这种怎么申请权限呢？

* 1、Info.plist 文件中，添加使用位置描述，Key为 `Privacy - Location When in Use Usage Description`

* 2、然后调用如下代码获取位置信息

  ```swift

    import CoreLocation


    /// 单次定位组件，获得位置信息后即自动关闭定位
    public class OnceGetLocationMgr: NSObject {
        
        /// 定位结果回调
        private var resultClosure: LocationResultClosure? = nil
        /// 是否正在定位
        private var isUpdatingLocation: Bool = false
        
        // 系统定位API
        private lazy var locationManager: CLLocationManager = {
            let mgr = CLLocationManager()
            mgr.delegate = self
            mgr.desiredAccuracy = kCLLocationAccuracyBest
            return mgr
        }()
        
        /// 开始定位
        public func startLocation(locationResultClosure: @escaping LocationResultClosure) {
            self.resultClosure = locationResultClosure
            startUpdatingLocation()
        }
        
        /// 关闭定位
        public func stopLocation() {
            stopUpdatingLocation()
            callback(result: .cancel, location: nil)
        }
    }

    // MARK: 系统定位代理
    extension OnceGetLocationMgr: CLLocationManagerDelegate {
        
        /// 成功获取定位
        public func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
            stopUpdatingLocation()
            callback(result: .success, location: locations.last)
        }
        
        /// 获取定位失败
        public func locationManager(_ manager: CLLocationManager, didFailWithError error: any Error) {
            stopUpdatingLocation()
            callback(result: .failed, location: nil)
        }
        
        /// 定位权限发生变化（实际没变化也会调用）
        public func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
            startUpdatingLocation()
        }
    }

    // MARK: 私有方法
    extension OnceGetLocationMgr {
        
        /// 增加 isUpdatingLocation，保证不会多次调用 locationManager.startUpdatingLocation
        /// 目前尚没发现多次调用会带来什么后果，但添加保护是没错的
        private func startUpdatingLocation() {
            if isUpdatingLocation {
                return
            }
            // 申请权限
            if locationManager.authorizationStatus == .notDetermined {
                locationManager.requestWhenInUseAuthorization()
                return
            }
            // 没有权限
            if locationManager.authorizationStatus == .denied || locationManager.authorizationStatus == .restricted {
                DispatchQueue.global().async {
                    // 判断没有权限是否是因为定位服务关闭了
                    if CLLocationManager.locationServicesEnabled() {
                        // 用户没有授权应用
                        self.callback(result: .permissionDenied, location: nil)
                    } else {
                        // 定位服务本身已关闭
                        self.callback(result: .serviceDisable, location: nil)
                    }
                }
                return
            }
            // 有权限
            isUpdatingLocation = true
            locationManager.startUpdatingLocation()
        }
        
        /// 停止定位
        private func stopUpdatingLocation() {
            locationManager.stopUpdatingLocation()
            isUpdatingLocation = false
        }
        
        /// 回调结果
        /// - Parameters:
        ///   - result: 结果
        ///   - location: 位置信息
        private func callback(result: LocationResult, location: CLLocation?) {
            DispatchQueue.main.async {
                self.resultClosure?(result, location)
                self.resultClosure = nil
            }
        }
    }


  ```

  * 3、 调用 `requestWhenInUseAuthorization` 方法，系统会弹出弹框提示授权，弹框里有3个选项：

  > 1) 允许一次：即当前APP只要不关闭，可以在前台使用位置
  >
  > 2) 使用期间：即使APP关闭了，再次打开，前台仍然可以使用位置
  > 
  > 3) 拒绝：APP就没有获取位置的权限了


  ## 2. 后台持续使用位置

  这种权限一般用于导航的场景，我们需要一直使用用户的位置，那么配置和流程都会比较复杂。

* 1、Info.plist 文件中，添加使用位置描述，Key为 `Privacy - Location When in Use Usage Description`。

* 2、添加后台定位的能力 `Background Modes` 选择 `Location updates`

* 3、代码层面调用 `requestWhenInUseAuthorization`申请使用期间就够了，不需要使用方法`requestAlwaysAuthorization`。始终允许的场景，我们基本用不到。授权弹框跟上面一致。

```swift

    import Foundation
    import CoreLocation

    /// 持续定位组件，需要完成以下配置
    /// 1、Info.plist 文件中设置 `Privacy - Location When in Use Usage Description`
    /// 2、增加后台定位能力， `Background Modes` 选择 `Location updates`
    public class ConstantlyGetLocationMgr: NSObject {
        
        /// 定位结果回调
        private var resultClosure: LocationResultClosure? = nil
        /// 是否正在定位
        private var isUpdatingLocation: Bool = false
        
        // 系统定位API
        private lazy var locationManager: CLLocationManager = {
            let mgr = CLLocationManager()
            mgr.delegate = self
            mgr.desiredAccuracy = kCLLocationAccuracyBest
            mgr.showsBackgroundLocationIndicator = true
            // 允许后台使用位置
            mgr.allowsBackgroundLocationUpdates = true
            return mgr
        }()
        
        /// 开始定位
        public func startLocation(locationResultClosure: @escaping LocationResultClosure) {
            self.resultClosure = locationResultClosure
            startUpdatingLocation()
        }
        
        /// 关闭定位
        public func stopLocation() {
            stopUpdatingLocation()
            callback(result: .cancel, location: nil)
        }
    }

    // MARK: 系统定位代理
    extension ConstantlyGetLocationMgr: CLLocationManagerDelegate {
        
        /// 成功获取定位
        public func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
            callback(result: .success, location: locations.last)
        }
        
        /// 获取定位失败
        public func locationManager(_ manager: CLLocationManager, didFailWithError error: any Error) {
            callback(result: .failed, location: nil)
        }
        
        /// 定位权限发生变化（实际没变化也会调用）
        public func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
            startUpdatingLocation()
        }
    }

    // MARK: 私有方法
    extension ConstantlyGetLocationMgr {
        
        /// 增加 isUpdatingLocation，保证不会多次调用 locationManager.startUpdatingLocation
        /// 目前尚没发现多次调用会带来什么后果，但添加保护是没错的
        private func startUpdatingLocation() {
            if isUpdatingLocation {
                return
            }
            // 申请权限
            if locationManager.authorizationStatus == .notDetermined {
                locationManager.requestWhenInUseAuthorization()
                return
            }
            // 没有权限
            if locationManager.authorizationStatus == .denied || locationManager.authorizationStatus == .restricted {
                DispatchQueue.global().async {
                    // 判断没有权限是否是因为定位服务关闭了
                    if CLLocationManager.locationServicesEnabled() {
                        // 用户没有授权应用
                        self.callback(result: .permissionDenied, location: nil)
                    } else {
                        // 定位服务本身已关闭
                        self.callback(result: .serviceDisable, location: nil)
                    }
                }
                return
            }
            // 有权限
            isUpdatingLocation = true
            locationManager.startUpdatingLocation()
        }
        
        /// 停止定位
        private func stopUpdatingLocation() {
            locationManager.stopUpdatingLocation()
            isUpdatingLocation = false
        }
        
        /// 回调结果
        /// - Parameters:
        ///   - result: 结果
        ///   - location: 位置信息
        private func callback(result: LocationResult, location: CLLocation?) {
            DispatchQueue.main.async {
                self.resultClosure?(result, location)
            }
        }
    }


```




