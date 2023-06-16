# Metal

## Metal介绍

通过 Metal框架可以直接访问设备的图形处理单元（GPU），应用程序可以利用GPU快速渲染复杂场景并运行计算任务。

基于Metal的框架包含：MetalFX、MetalKit、MetalPerformaceShaders；

* MetalFX：比原生渲染效率更高；
* MetalKit：简化了屏幕上显示Metal内容的任务
* Metal Performance Shaders：提供了一个大型的优化计算和渲染着色器库，可以利用每个GPU的独特硬件。

## MSL

即着色器语言，全称 `Metal Shading Language`，是 C++的变体，通过 `MSL` 可以将方法运行到 `GPU` 上，着色器代码文件后缀为 `.metal`。


## 示例

下面我们通过官方提供的示例，来逐渐了解 Metal。

### 1、计算两个长度一致的浮点型数组，按位相加的结果存放到第三个数组里。

这是一个相对比较简单的运算，如果采用C语言来计算，代码一般如下


```c
void add_arrays(const float* inA,
                const float* inB,
                float* result,
                int length)
{
    // 这里通过 for 循环按位取出，然后相加
    for (int index = 0; index < length ; index++)
    {
        result[index] = inA[index] + inB[index];
    }
}
```

那么用着色器语言（MSL，即 Metal Shading Language）怎么写呢？

```c
#include <metal_stdlib>
using namespace metal;

/// MSL代码，两个数组中的浮点型数据相加，然后存到第三个数组中
/// kernel 关键字，声明该函数是公共GPU函数，公共函数是应用程序可以看到的唯一函数，公共函数也不能被其他着色器调用
/// thread_position_in_grid 线程网格，使用线程网格进行计算
/// 函数使用 device 关键字声明其三个参数，表示这些指针位于设备地址空间中。 MSL 为内存定义了几个不相交的地址空间。 无论何时在 MSL 中声明指针，都必须提供关键字来声明其地址空间。 使用设备地址空间来声明 GPU 可以读取和写入的持久内存
kernel void add_arrays(device const float* inA,
                       device const float* inB,
                       device float* result,
                       uint index [[thread_position_in_grid]])
{
    // 我们这里则不需要，由于每组计算互不干扰，所以该函数将由计算网格中的多个线程调用
    result[index] = inA[index] + inB[index];
}
```

好的，我们已经知道 `MSL` 和 `C语言` 写法上的区别了，那么 `MSL` 该如何调用呢？

* 第一步：查找 GPU

```c
// MTLDevice 对象是对 GPU 的精简抽象；我们可以使用它来与 GPU进行通信
// Metal 会为每个 GPU 创建一个 MTLDevice，我们可以通过调用下面的方法获取默认的设备对象
// 一台mac可以有多个 GPU，我们可以通过 MTLCopyAllDevices 方法获取所有的设备对象
id<MTLDevice> device = MTLCreateSystemDefaultDevice();
_mDevice = device
```

* 第二步：获取 MSL函数的引用
  

```c
// 构建程序时，xcode会将后缀为 .metal 的文件中的 MSL函数 编译到应用程序中嵌入的默认 Metal库中
// 所以我们可以通过 MTLLibrary 来加载默认 Metal库
id<MTLLibrary> defaultLibrary = [_mDevice newDefaultLibrary];

if (defaultLibrary == nil)
{
    NSLog(@"Failed to find the default library.");
    return nil;
}

// 然后通过 newFunctionWithName 方法来说 MSL函数
id<MTLFunction> addFunction = [defaultLibrary newFunctionWithName:@"add_arrays"];

if (addFunction == nil)
{
    NSLog(@"Failed to find the adder function.");
    return nil;
}
```

* 第三步：准备管道

```c
NSError *error = nil;

// MSL 函数并不是可执行代码，可以通过创建管道，将函数转为可执行代码，该操作会相对耗时，不要在对性能要求较高的地方创建管道
// 管道指定了 GPU 为完成特定任务而执行的步骤
// 在 Metal 中，管道由管道状态表示
_mAddFunctionPSO = [_mDevice newComputePipelineStateWithFunction: addFunction error:&error];

if (_mAddFunctionPSO == nil)
{
    NSLog(@"Failed to created pipeline state object, error %@.", error);
    return nil;
}
```

* 第四步：创建命令队列
  
```c
// 发送任务给到GPU，我们需要创建一个命令队列，命令队列用来调度命令
_mCommandQueue = [_mDevice newCommandQueue];

if (_mCommandQueue == nil)
{
    NSLog(@"Failed to find the command queue.");
    return nil;
}
```

* 第五步：创建数据缓存区，并往缓存区中塞入数据

```c
// _mBufferA和_mBufferB是输入缓存区，_mBufferResult为输出缓存区
_mBufferA = [_mDevice newBufferWithLength:bufferSize options:MTLResourceStorageModeShared];
_mBufferB = [_mDevice newBufferWithLength:bufferSize options:MTLResourceStorageModeShared];
_mBufferResult = [_mDevice newBufferWithLength:bufferSize options:MTLResourceStorageModeShared];

// 向缓存区中填入随机数
// 向缓存区中写入数据，需要保证数据类型和MSL的类型一致
// 写数据到缓存区，是在CPU中操作的
[self generateRandomFloatData:_mBufferA];
[self generateRandomFloatData:_mBufferB];
```

* 第六步：创建命令缓存
  
```c
// 直接通过命令队列来创建命令缓存
id<MTLCommandBuffer> commandBuffer = [_mCommandQueue commandBuffer];
```

* 第七步：创建命令编码器

```c
// 要将命令写入命令缓存区，需要使用命令编码器
// 我们当前示例中，使用的是计算命令编码器，当然还有其他类型的命令解码器
id<MTLComputeCommandEncoder> computeEncoder = [commandBuffer computeCommandEncoder];
```

* 第八步：设置管道状态和参数


```c
// 要对命令进行编码，需要调用编码器的一系列方法：
// 1、设置管道状态
// 2、设置数据缓存区（其中输入缓存区中已经有数据了），我们看到 atIndex有顺序，这里是顺序和MSL中是一致的
// 具体步骤可参照下图
[computeEncoder setComputePipelineState:_mAddFunctionPSO];
[computeEncoder setBuffer:_mBufferA offset:0 atIndex:0];
[computeEncoder setBuffer:_mBufferB offset:0 atIndex:1];
[computeEncoder setBuffer:_mBufferResult offset:0 atIndex:2];
```

![Alt text](https://docs-assets.developer.apple.com/published/18d160d8f2/Metal-CreatingMetalApp@2x.png)


* 第九步：组织线程网格
  
```c
// Metal支持创建 1维、2维和3维网格，在本例中，由于 add_arrays函数中使用的是1维数组，因此此示例创建的1维网格为 （arrayLength x 1 x 1）
MTLSize gridSize = MTLSizeMake(arrayLength, 1, 1);
```

* 第十步：指定线程组大小
  
```c
// Metal将网格细分为更小的网格，称为线程组。每个线程组都是单独计算的。Metal可以将线程组分派给GPU上不同的处理元素，以加快处理速度
// 向管道状态对象询问最大可能得线程组，如果该数量大于数据集的大小，则保持跟数据集一致，否则就采用最大线程数，这取决于用于创建管道状态对象的函数的复杂性
// 单个线程组的最大线程数
NSUInteger threadGroupSize = _mAddFunctionPSO.maxTotalThreadsPerThreadgroup;
if (threadGroupSize > arrayLength)
{
    threadGroupSize = arrayLength;
}
MTLSize threadgroupSize = MTLSizeMake(threadGroupSize, 1, 1);

```

* 第十一步：编码计算命令

```c
// 这里其实还不是很懂：什么叫把网格分为更小的网格，意思是 gridSize中的数据会被分配到 threadgroupSize 的线程中进行执行吗？
// 我实际跑了一下模拟器，threadgroupSize的大小为1024，但是此例中grideSize为 (width = 16777216, height = 1, depth = 1)，是不是说 16777216次计算被分配到1024个线程中执行
[computeEncoder dispatchThreads:gridSize threadsPerThreadgroup:threadgroupSize]
```

* 第十二步：结束计算通道

```c
// 当我们没有更多命令添加到计算通道时，关闭计算通道
[computeEncoder endEncoding];
```

* 第十三步：提交buffer，执行命令

```c
// 通过将命令缓冲区提交到队列来运行命令缓冲区中的命令。
// 提交缓存区后，Metal会异步准备要执行的命令，然后安排命令缓存区在GPU上执行
[commandBuffer commit];
```

* 第十四步：阻塞等待计算完成

```c
// 会阻塞当前CPU线程，直到 GPU 命令执行完毕
[commandBuffer waitUntilCompleted];
```

当然我们也可以通过向命令缓存区中添加异步回调避免阻塞当前线程。

```c
// MTLCommandBuffer中的方法
 - (void)addCompletedHandler:(MTLCommandBufferHandler)block
 ```
  

参考文档：https://developer.apple.com/documentation/metal/performing_calculations_on_a_gpu




### 2、创建一个MetalKit视图和渲染通道来绘制视图的内容。



参考文档：https://developer.apple.com/documentation/metal/using_metal_to_draw_a_view_s_contents



### 2、渲染一个简单的2D三角形。

参考文档：https://developer.apple.com/documentation/metal/using_a_render_pipeline_to_render_primitives





## TODO

* 暂时还不清楚SpriteKit、SceneKit与Metal之间的关系。


## 其他参考文档

https://juejin.cn/post/6844903619339223048

https://blog.csdn.net/Philm_iOS/article/details/81004596


