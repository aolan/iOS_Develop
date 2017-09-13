# Swift中延迟加载

## Objective-c中延迟加载
```obj-c
@property(nonatomic, strong) UILabel *nameLbl;
	
- (UILabel *)nameLbl{
	if(!_nameLbl){
	   _nameLbl = [[UILabel alloc] init];
	}
	return _nameLbl;
}	
```


## Swift中延迟加载

```obj-c
//在属性定义的地方，使用闭包来实例化属性
lazy var nameLbl:UILabel? = {
	var tmpLbl = UILabel()
	tmpLbl.textColor = UIColor.blackColor()
	return tmpLbl
}()
```
	
	
