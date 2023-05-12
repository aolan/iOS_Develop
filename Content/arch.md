时间比较长了，再次回顾一下 `Architectures`的含义

## Architectures

当前设备环境下支持的指令架构。真机环境下是 arm64、armv7（现在很少用到了）。模拟器环境下是 arm64（M1芯片）、X86_64、i386（后两者也很少用到了）

## Build Active Architecture Only

是否只编译当前设备的质量架构。一般 Debug环境，为了提高编译速度，会设置为 Yes；Release环境，应该设置为 No，编译所有类型设备的指令架构

## Excluded Architecture

排除的架构，不参与编译。

## VALID_ARCHS

用户自己设置的可用架构。这个选项在`User-Defined`里面，它的优先级是最高的。如果Architectures支持了很多架构，但是VALID_ARCHS只填写了一小部分，那么实际上支持的架构也是这一小部分。所以VALID_ARCHS的值一般和Architectures保持一致。

## EXCLUDED_ARCHS

在Xcode12的release note中，VALID_ARCHS已经不推荐使用了(deprecate)，我们应该使用 `EXCLUDED_ARCHS`；这个 `EXCLUDED_ARCHS` 和 `Excluded Architecture`是同一个意思，所以我们使用`Excluded Architecture`就好了。


## More

此外还有更为详细的设备属于哪个指令集的信息、如何合并拆分指令集的教程，都是参照下面的教程。



参考：https://juejin.cn/post/7034782069410496542
