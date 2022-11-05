# Swift(do-try-catch、try?、try!)

## try

需要配合 `do-catch` 使用，否则编译不过，如下面的代码，就无法编译通过

```swift

let path = "不存在的文件路径"
try FileManager.default.removeItem(path)

```

如果要编译通过，可以这样

```swift

let path = "不存在的文件路径"
do {
    try FileManager.default.removeItem(path)
    return true // 表示删除成功
} catch let error {
    print(error)
    return false // 表示删除失败
}

```

## try?

`try?` 一般不需要通过 `do-catch` 包裹。一般我们不关注错误原因时，可以用 `try?`，代码更整洁。例如：

```swift

let path = "不存在的文件路径"
try? FileManager.default.removeItem(path)

```

当然，如果我们想根据执行结果来判断后续流程，也是可以的，只是方法 `removeItem` 没有返回值，对于有返回值的，一般这样写：

```swift

let jsonString = "{\"name\"::\"zhangsan\"}"   // 很显然，json字符串多了一个冒号
let data = jsonString.data(using: .utf8)
// 这一句，判断json解析的结果是否有值，如果为nil，就直接返回了，不会执行到下面 print 语句
guard let json = try? JSONSerialization.jsonObject(with: data!, options: []) else { return }
print("---------------\(json)")

```


## try!（慎用，只有确保不会出错时才可使用）

对于调用可能抛出异常的方法，不能使用 `try!`，否则程序会崩溃，例如：

```swift

let path = "不存在的文件路径"
try! FileManager.default.removeItem(path)

```

即使用 `do-catch` 包裹，也是会崩溃的，如

```swift

let path = "不存在的文件路径"
do {
    try! FileManager.default.removeItem(path)
} catch let err {
    // IDE提示该方法不会被调用，所以也就捕捉不到错误了
}

```


## 注意事项

以上三种方法并不能处理所有的崩溃，比如数组下标越界，这种是不会抛异常的，即使用 `do-catch` 包裹，也依然会崩溃。

```swift
let list = [1, 2]
do {
    try list[2]   // 仍然会崩溃在这一行
} catch let err {
    print(error)
}

```

