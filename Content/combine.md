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

## 2.1 Mappping elements 映射元素

### 2.1.1 scan
### 2.1.2 tryScan
### 2.1.3 setFailureType
### 2.1.4 map
### 2.1.5 tryMap
### 2.1.6 flatMap

## 2.2 Filtering elements 筛选元素

### 2.2.1 compactMap
### 2.2.2 tryCompactMap
### 2.2.3 replaceEmpty
### 2.2.4 filter
### 2.2.5 tryFilter
### 2.2.6 replaceError
### 2.2.7 removeDuplicates
### 2.2.8 tryRemoveDuplicates

## 2.3 Reducing elements 还原元素
### 2.3.1 collect
### 2.3.2 reduce
### 2.3.3 tryReduce
### 2.3.4 ignoreOutput

## 2.4 Mathematic operations on elements 元素的数学运算
### 2.4.1 max
### 2.4.2 tryMax
### 2.4.3 count
### 2.4.4 min
### 2.4.5 tryMin

## 2.5 Applying matching criterial to elements 条件匹配应用于元素
### 2.5.1 allSatisfy
### 2.5.2 tryAllSatisfy
### 2.5.3 contains
### 2.5.4 containsWhere
### 2.5.5 tryContainsWhere

## 2.6 Applying sequence operations to elements 对元素进行序列操作
### 2.6.1 firstWhere
### 2.6.2 tryFirstWhere
### 2.6.3 first
### 2.6.4 lastWhere
### 2.6.5 tryLastWhere
### 2.6.6 last
### 2.6.7 dropWhile 
### 2.6.8 tryDropWhile 
### 2.6.9 dropUntilOutput
### 2.6.10 prepend 
### 2.6.11 drop
### 2.6.12 prefixUntilOutput
### 2.6.13 prefixWhile
### 2.6.14 tryPrefixWhile
### 2.6.15 output

## 2.7 Combining elements from multiple publishers 组合多个发布者元素
### 2.7.1 combineLatest
### 2.7.2 merge
### 2.7.3 zip

## 2.8 Handling errors 处理错误
### 2.8.1 catch
### 2.8.2 tryCatch
### 2.8.3 assertNoFailure
### 2.8.4 retry
### 2.8.5 mapError

## 2.9 Adapting publisher types 调整发布者类型
### 2.9.1 switchToLatest
### 2.9.2 eraseToAnyPublisher

## 2.10 Controlling timing 控制时序
### 2.10.1 debounce
### 2.10.2 delay
### 2.10.3 measureInterval
### 2.10.4 throttle
### 2.10.5 timeout

## 2.11 Encoding and decoding 编解码
### 2.11.1 encode
### 2.11.2 decode

## 2.12 Working with multiple subscribers 与多个订阅者合作
### 2.12.1 multicase

## 2.13 Debugging
### 2.13.1 breakpoint
### 2.13.2 handleEvents
### 2.13.3 print


# 3. Subjects 特殊的发布者

* Subjects 是一种遵循 Subject 协议的特殊的发布者。 这个协议要求 subjects 有一个 .send(_:) 方法，来允许开发者发送特定的值给订阅者或管道。
* Subjects 可以通过调用 .send(_:) 方法来将值“注入”到流中， 这对于将现有的命令式的代码与 Combine 集成非常有用。
* 一个 subject 还可以向多个订阅者广播消息。 如果多个订阅者连接到一个 subject，它将在调用 send(_:) 时向多个订阅者发送值。 一个 subject 还经常用于连接或串联多个管道，特别是同时给多个管道发送值时。














参考：https://heckj.github.io/swiftui-notes/index_zh-CN.html
