#setNeedsDisplay和setNeedsLayout异同


##相同点

两个方法都是异步执行的。


##不同点

* setNeedsDisplay会调用drawRect:方法，方便绘图。

	1.drawRect:是在viewDidLoad之后开始调用的；
	
	2.如果在UIView初始化时没有设置rect，则drawRect:不会被调用;
			
	3.该方法在调用sizeToFit后被调用，所以可以先调用sizeToFit计算出size。然后系统自动调用drawRect:方法;
	
	4.通过设置contentMode属性值为UIViewContentModeRedraw。那么将在每次设置或更改frame的时候自动调用drawRect:;【不提倡】
	
	5.直接调用setNeedsDisplay，或者setNeedsDisplayInRect:触发drawRect:，但是有个前提条件是rect不能为0。【不提倡】
	

* setNeedsLayout会调用layoutSubviews方法，方便展示数据。

		
	1.init初始化不会触发layoutSubviews;
	
	2.addSubview会触发layoutSubviews;
	
	3.View的Frame发生变化时会触发layoutSubviews;
	
	4.滚动一个UIScrollView会触发layoutSubviews;
	
	5.旋转Screen会触发父UIView上的layoutSubviews事件。
	
	6.改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件。
	
	7.直接调用setLayoutSubviews。
	
		
##使用drawRect方法应注意

	1、若使用UIView绘图，只能在drawRect：方法中获取相应的contextRef并绘图。如果在其他方法中获取将获取到一个invalidate的ref并且不能用于画图。drawRect：方法不能手动显示调用，必须通过调用setNeedsDisplay 或者 setNeedsDisplayInRect，让系统自动调该方法。
	2、若使用calayer绘图，只能在drawInContext: 中（类似于drawRect）绘制，或者在delegate中的相应方法绘制。同样也是调用setNeedDisplay等间接调用以上方法
	3、若要实时画图，不能使用gestureRecognizer，只能使用touchbegan等方法来掉用setNeedsDisplay实时刷新屏幕

