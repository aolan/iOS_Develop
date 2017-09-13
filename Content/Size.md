# sizeToFit && sizeThatFits

## sizeToFit

	直接修改view的bounds。
	


## sizeThatFits

	返回最佳的size，但是不直接修改view的bounds。
	
利用这个方法，可以做一些UILabel宽度高度自适应的工作，例如代码：
	
	UILabel *label = [[UILabel alloc] init];
	label.font = [UIFont systemFontOfSize:15];
	label.text = @"可以做一些UILabel宽度高度自适应的工作";
	
	CGSize bestSize = [label sizeThatFits:CGSizeZero];
	label.frame = CGRectMake(0, 0, bestSize.width, bestSize.height);
