# OCMock


## 恶补运行时知识

```objc

// 获取对象的所属的类，如果obj传入的是对象，则返回的是该对象所属的类
// 如果obj传入的是类，则返回的是该类的元类
Class object_getClass(id obj);

// 根据类名获取该类
Class objc_getClass(const char * name);

// 根据类名返回该类的元类
Class objc_getMetaClass(const char * name);



// 返回指定类的实例方法
Method class_getInstanceMethod(Class cls, SEL name);

// 返回指定类的类方法
Method class_getClassMethod(Class cls, SEL name);

// 如果obj传入的是实例对象，则会修改实例对象所属的类
// 如果obj传入的是类，则会修改类的元类
Class object_setClass(id obj, Class cls);

// 给类增加新的方法
BOOL class_addMethod(Class cls, SEL name, IMP imp, const char * types);

// 替换方法实现
IMP class_replaceMethod(Class cls, SEL name, IMP imp, const char * types) 

// 向运行时注册一个SEL选择器
SEL sel_registerName(const char * str);


/*
* 用NSInvocation同步调用方法，类似于 [@"Hello" stringByAppendingString:@" World!"];
* 设置参数时，下标从2开始，因为0和1已经被sel和target占用了，有点像js的参数
*/
NSString *string = @"Hello";
NSString *anotherString;
NSString *stringToAppend = @" World!";
SEL sel = @selector(stringByAppendingString:);
NSMethodSignature *sig = [NSString instanceMethodSignatureForSelector: sel];
NSInvocation *inv = [NSInvocation invocationWithMethodSignature: sig];
[inv setTarget: string];
[inv setArgument:&stringToAppend atIndex:2];
[inv invoke];
[inv getReturnValue:&anotherString];

/*
* 获取类的实现，如果该类没有实现，则会返回类似于libobjc.A.dylib`_objc_msgForward这样的结果
* 1. 类方法的实现是在类所属的元类中
* 2. 实例方法的实现是在类中
*/ 

IMP class_getMethodImplementation(Class cls, SEL name);


```
