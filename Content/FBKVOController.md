
# FBKVOController

FBKVOController是Facebook的开源库，在使用的时候注意循环引用。

```objc 

#import "KVOController.h"

@interface TestObject()

@property (nonatomic, strong) NSObject *obj;
@property (nonatomic, strong) UIView *viewObj;

@end

@implementation TestObject

- (void)observer
{
  // 没有问题，只能观察子属性的属性
  [self.kvoController observe:self.viewObj keyPath:"frame"
                      options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionInitial
                        block:NULL];
  // 会导致循环引用                    
  [self.kvoController observe:self keyPath:"obj"
                      options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionInitial
                        block:NULL];

}


                          
@end




```
