![image](https://github.com/aolan/iOS_Develop/assets/6744261/254bdbfe-eb70-467e-a513-17694d03ae5b)

* 1、当调用 .subscribe(_: Subscriber) 时，订阅者被连接到了发布者。
* 2、发布者随后调用 receive(subscription: Subscription) 来确认该订阅。
* 3、在订阅被确认后，订阅者请求 N 个值，此时调用 request(_: Demand)。
* 4、发布者可能随后（当它有值时）发送 N 个或者更少的值，通过调用 receive(_: Input)。 发布者不会发送超过需求量的值。
* 5、订阅确认后的任何时间，订阅者都可能调用 .cancel() 来发送 cancellation
* 6、发布者可以选择性地发送 completion：receive(completion:)。 完成可以是正常终止，也可以是通过 .failure 完成，可选地传递一个错误类型。 已取消的管道不会发送任何完成事件。

# 1. Publisher 发布者

Combine提供了多种便捷的发布者：

* Just
* Future
* Deferred
* Empty
* Sequence
* Fail
* Record
* Share
* Multicast
* ObservableObject
* @published

此外，Foundation也提供了一些列发布者：

* URLSession.dataTaskPublisher
* .publisher on KVO instance
* NotificationCenter
* Timer
* Result

## 1.1 Just（单一数据源，可用于链式逻辑判断）

* 简单示例
  
```swift
let _ = Just(5).map { value -> String in
    print(value)
    return "a string"
}.sink { value in
    print(value)
}
```

* 增加了条件判断
  
```swift
let _ = Just(5).map { value -> String in
    switch value {
    case _ where value < 1:
        return "none"
    case _ where value == 1:
        return "one"
    case _ where value == 2:
        return "couple"
    case _ where value == 3:
        return "few"
    case _ where value > 8:
        return "many"
    default:
        return "some"
    }
}.sink { receivedValue in
    print("The end result was \(receivedValue)")
}
```


## 1.2 Future（异步函数）
## 1.3 Deferred
## 1.4 Empty
## 1.5 Sequence
## 1.6 Fail
## 1.7 Record
## 1.8 Share
## 1.9 Multicast

## 1.10 @ObservableObject（配合 SwiftUI 使用）

## 1.11 @Published（配合 SwiftUI 使用）


# 2. Operator 操作符（非常多）

操作符是 Apple 参考文档中发布者下包含的一些预构建函数的便捷名称。 操作符用来组合成管道。 许多操作符会接受开发人员的一个或多个闭包，以定义业务逻辑，同时保持并持有发布者/订阅者的生命周期。
一些操作符支持合并来自不同管道的输出、更改数据的时序或过滤所提供的数据。 操作符可能还会对操作类型有限制， 还可用于定义错误处理和重试逻辑、缓冲和预先载入以及支持调试。

## 2.1 Mappping elements

### 2.1.1 scan
### 2.1.2 tryScan
### 2.1.3 setFailureType
### 2.1.4 map
### 2.1.5 tryMap
### 2.1.6 flatMap




参考：https://heckj.github.io/swiftui-notes/index_zh-CN.html
