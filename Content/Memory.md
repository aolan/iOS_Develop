# 三种代理的写法是否有问题

	* 1、@property(nonatomic,assign) id <BDelegate> delegate
	* 2、@property(nonatomic,strong) id <BDelegate> delegate
	* 3、@property(nonatomic,weak) id <BDelegate> delegate

第一种情况，会有可能产生崩溃，这是由于B在调用A类的具体实现的时候，A可能已经被释放了，这个时候delegate就是垂悬指针，调用A的方法就会崩溃；

第二种情况，会有可能导致循环引用，最好不要这么写；

第三种情况，正确。ARC模式下使用weak比较安全，当A被释放的时候，delegate会自动置为nil，向nil发送消息并不会导致程序崩溃。

从上面可以看出assgin和weak的区别，assgin仅仅是缺少一步操作，就是将垂悬指针置为nil，所以需要开发者在A的delloc方法中把b.delegate置为nil
