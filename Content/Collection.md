

# NSSet 常用方法总结

方法 | 描述 |
------------ | ------------- | 
+(id)setWithObjects:obj1,obj2,...nil	 | 使用一组对象创建新的集合 
-(id)initWithObjects:obj1,obj2,....nil  | 使用一组对象初始化新分配的集合
-(NSUInteger)count|    返回集合成员个数
-(BOOL)containsObject:obj |  确定集合是否包含对象 obj
-(BOOL)member:obj | 确定集合是否包含对象 obj
-(NSEnumerator*)objectEnumerator | 返回集合中所有对象到一个 NSEnumerator 类型的对象
-(BOOL)isSubsetOfSet:nsset | 判断集合是否是NSSet的子集
-(BOOL)intersectsSet:nsset | 判断两个集合的交集是否至少存在一个元素
-(BOOL)isEqualToSet:nsset | 判断两个集合是否相等

# NSMutableSet 常用方法总结
方法 | 描述 |
------------ | ------------- | 
-(id)setWithCapcity:size | 创建一个有size大小的新集合
-(id)initWithCapcity:size | 初始化一个新分配的集合，大小为size
-(void)addObject:obj | 添加对象 obj 到集合中
-(void)removeobject:obj | 从集合中删除对象 obj
-(void)removeAllObjects | 删除集合中所有对象
-(void)unionSet:nsset | 将nsset的所有元素添加到集合
-(void)minusSet:nsset | 从集合中去掉所有的NSSet 的元素
-(void)interectSet:nsset | 集合和NSSet 做交集运算

优点

1. 在已知一个对象时，想知道这个对象是否在集合中，使用NSSet效率高。
2. 判断集合A是否是集合B的子集，可以使用 [A isSubsetOfSet:B]。
3. 判断集合A和集合B是否有交集，可以使用[A intersects:B]。



# NSPointerArray

指针数组，用起来跟NSMutableArray类似，可以存储强/弱引用对象，也可以存储普通的C指针。



# NSHashTable



# NSMapTable

NSMapTable 可以处理的 ```key -> obj ```式映射如 ```NSDictionary```，但它也可以处理 ```obj -> obj``` 的映射 - 也被称为 “关联数组” 或简称为 “map”。

```objc

// 这种跟NSMutableDictionary的效果是一样的
NSMapTable *keyToObjectMapping = [NSMapTable mapTableWithKeyOptions:NSMapTableCopyIn valueOptions:NSMapTableStrongMemory];

// 对象到对象的映射
NSMapTable *objectToObjectMapping = [NSMapTable mapTableWithStrongToStrongObjects];

```


