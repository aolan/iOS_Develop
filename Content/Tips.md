
1. 宏定义的时候，如果内容较多，可以采用do{}while(0)来囊括代码，好处是存在一个独立的block，可以用来进行变量定义，进行比较复杂的实现，不会与上下文发生混淆。

```objc

#define NSAssert(condition, desc, ...)	\
    do {				\
	__PRAGMA_PUSH_NO_EXTRA_ARG_WARNINGS \
	if (__builtin_expect(!(condition), 0)) {		\
            NSString *__assert_file__ = [NSString stringWithUTF8String:__FILE__]; \
            __assert_file__ = __assert_file__ ? __assert_file__ : @"<Unknown File>"; \
	    [[NSAssertionHandler currentHandler] handleFailureInMethod:_cmd \
		object:self file:__assert_file__ \
	    	lineNumber:__LINE__ description:(desc), ##__VA_ARGS__]; \
	}				\
        __PRAGMA_POP_NO_EXTRA_ARG_WARNINGS \
    } while(0)


```



2. 忽略部分编译器警告，像QQ SDK的初始化就很奇葩，有返回值，但是我们并不需要。如果不加上忽略警告的宏，编译器就会报警告。

```objc

#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wunused"
    // 注册QQ
    [[TencentOAuth alloc] initWithAppId:kQQ_SDK_APPID andDelegate:nil];
#pragma clang diagnostic pop

```

3. __has_include 判断是否包含某个库

```objc

#if __has_include(<YTKNetwork/YTKNetwork.h>)
    NSLog(@"包含YTKNetwork库");
#else
    NSLog(@"不包含YTKNetwork库");
#endif

```

4. `FOUNDATION_EXPORT` 这个关键字来定义字符串，那么和 #define 的区别在哪里呢：[参考链接](https://www.jianshu.com/p/0694eb214d87)

 * `FOUNDATION_EXPORT` 检测字符串的值是否相等的时候更快。可以直接使用(stringInstance == MyFirstConstant)来比较，比较的是指针地址。
 * `define` 则是使用 ([stringInstance isEqualToString: MyFirstConstant])来比较的，按照字符遍历比较。


5. `NSProxy`可以用于模拟多继承，[参考链接](http://ios.jobbole.com/87856/)

```objc

// 具体的原理是，NSProxy是一个虚类，可以通过继承它，重写下面的两个方法以实现消息转发到另一个实例
// 使用场景：可以通过集成NSProxy实现消息的转发，相当于一个集约式的管理中心

- (void)forwardInvocation:(NSInvocation *)anInvocation
{

}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel
{
    
}

```

