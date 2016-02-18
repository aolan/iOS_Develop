#Category/Extension

##Category作用


* 1、可以减少单个文件的体积；
* 2、可以把不同的功能组织到不同的文件里；
* 3、可以又多个开发者共同完成一个类；
* 4、可以按照需要加载想要的category。

##Extension作用

* 1、可以根据功能合理有效的组织代码；
* 2、声明私有方法。

##区别

* 1、extension相当于一个匿名的category，extension是在编译期决议，就是类的一部分。一般用来隐藏私有信息，必须有类的源码才能为一个类添加extension；
* 2、category是在运行期决议的，所以无法添加实例变量。如果需要添加可以采用关联对象的方式进行。

##Category方法覆盖

如果一个类有两个拓展category，并且都有相同的方法，这个和继承不一样，并不是完全的替换原来类的同名方法，只是category的该方法在方法列表的前面而已，因此如果想要找到原来的方法，只需要获取到methodList中的最后一个方法即可。代码如下：

	Class currentClass = [MyClass class];
	MyClass *my = [[MyClass alloc] init];
	
	if (currentClass) {
	    unsigned int methodCount;
	    Method *methodList = class_copyMethodList(currentClass, &methodCount);
	    IMP lastImp = NULL;
	    SEL lastSel = NULL;
	    for (NSInteger i = 0; i < methodCount; i++) {
	        Method method = methodList[i];
	        NSString *methodName = [NSString stringWithCString:sel_getName(method_getName(method)) 
	                                        encoding:NSUTF8StringEncoding];
	        if ([@"printName" isEqualToString:methodName]) {
	            lastImp = method_getImplementation(method);
	            lastSel = method_getName(method);
	        }
	    }
	    typedef void (*fn)(id,SEL);
	
	    if (lastImp != NULL) {
	        fn f = (fn)lastImp;
	        f(my,lastSel);
	    }
	    free(methodList);
	}


##拓展

这个命令可以将.m文件编译成cpp文件

	clang -rewrite-objc MyClass.m
	
	
参考 : http://tech.meituan.com/DiveIntoCategory.html
