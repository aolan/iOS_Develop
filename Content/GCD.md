# GCD全局调度队列(Global Dispatch Queues)

## 队列种类

* 1、主线程的 *main queue* ，通过 *dipatch_get_main_queue* 获取；
* 2、并行队列 *global dispatch queue* ，通过 *dispatch_get_global_queue* 获取，由系统创建，有三种不同的优先级，并行队列的执行顺序与其加入队列的顺序相同。
* 3、串行队列 *serial queue* ，一般用于按顺序同步访问，可创建任意数量的串行队列，各个串行队列之间是并发的，一般用 *dispatch_queue_create* 来创建，非Arc的情况下需要手动释放队列。


## 调度方式

* 1、*dispatch_async* 异步执行
* 2、*dispatch_sync*  同步执行
* 3、*dispatch_after* 延迟执行
* 4、*dispatch_once*  一次执行


## 合并汇总

*dispatch_group_t* 示例代码：

	-(void)action8{
 
    	dispatch_group_t group = dispatch_group_create();
    
    	dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
        	for (int i = 0; i < 5; i++) NSLog(@"并行执行的线程一=============%d",i);
    	});
    
    	dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
        	for (int i = 0; i < 5; i++) NSLog(@"并行执行的线程二=============%d",i);
		});
    
	    dispatch_group_notify(group, dispatch_get_global_queue(0, 0), ^{
       	 	NSLog(@"汇总结果===========");
    	});
    
    	dispatch_release(group);
	}
	
	
	
输入结果：

	2013-12-10 11:48:39.139 TestGCD[699:1403] 并行执行的线程一=============0
	2013-12-10 11:48:39.139 TestGCD[699:3807] 并行执行的线程二=============0
	2013-12-10 11:48:39.143 TestGCD[699:1403] 并行执行的线程一=============1
	2013-12-10 11:48:39.143 TestGCD[699:3807] 并行执行的线程二=============1
	2013-12-10 11:48:39.144 TestGCD[699:1403] 并行执行的线程一=============2
	2013-12-10 11:48:39.144 TestGCD[699:3807] 并行执行的线程二=============2
	2013-12-10 11:48:39.145 TestGCD[699:1403] 并行执行的线程一=============3
	2013-12-10 11:48:39.145 TestGCD[699:3807] 并行执行的线程二=============3
	2013-12-10 11:48:39.146 TestGCD[699:3807] 并行执行的线程二=============4
	2013-12-10 11:48:39.146 TestGCD[699:1403] 并行执行的线程一=============4
	2013-12-10 11:48:39.147 TestGCD[699:3807] 汇总结果===========
	
	
参考 ： http://blog.sina.com.cn/s/blog_a3b58cbe0101ekz6.html
