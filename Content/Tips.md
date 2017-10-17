
1. 宏定义的时候，如果内容较多，可以采用do{}while(0)来囊括代码，好处是存在一个独立的block，可以用来进行变量定义，进行比较复杂的实现，不会与上下文发生混淆。

```

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
