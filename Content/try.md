# Swift(do-try-catch)

## try!

对于调用可能抛出异常的方法，不能使用 try!，否则程序会崩溃，例如：

```swift

let path = "不存在的文件路径"
try! FileManager.default.removeItem(path)

```

即使用 do-catch 包裹，也是会crash的，如

```swift

let path = "不存在的文件路径"
do {
    try! FileManager.default.removeItem(path)
} catch let err {
    // IDE提示该方法不会被调用，所以也就捕捉不到错误了
}

```

## try?

如果对所调用的方法，不关注调用该方法导致的异常，可以使用 try? 来修饰，例如：

```swift

let path = "不存在的文件路径"
try? FileManager.default.removeItem(path)

```

如果想根据异常来判断后续的代码流程，就要用到 do-try?-catch了，例如：

```swift

let path = "不存在的文件路径"
do {
    try? FileManager.default.removeItem(path)
} catch let error {
    print(error)
}

因为 removeItem 没有返回值，我们不能通过返回值直接判断结果，再举一个例子，这个就是可以的。

```swift

let jsonString = "{\"name\":\"zhangsan\"}"
let data = jsonString.data(using: .utf8)
guard let json = try? JSONSerialization.jsonObject(with: data!, options: []) else { return }
print("---------------\(json)")

```
假设 json 字符串格式不正确，这里我们可以通过 try? 后面方法的返回值来判断后续代码走向。


## 注意事项

do-try-catch 并不是万能的，比如数组下标越界，这种是不会抛异常的，即使用 do-catch 包裹，也依然会崩溃。

```swift
let list = [1, 2]
do {
    try? list[2]   // 仍然会崩溃在这一行
} catch let err {
    print(error)
}

```

