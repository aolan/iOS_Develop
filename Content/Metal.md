# Metal

## 概览

通过 Metal框架可以直接访问设备的图形处理单元（GPU），应用程序可以利用GPU快速渲染复杂场景并运行计算任务。

基于Metal的框架包含：MetalFX、MetalKit、MetalPerformaceShaders；


* MetalFX：比原生渲染效率更高；
* MetalKit：简化了屏幕上显示Metal内容的任务
* Metal Performance Shaders：提供了一个大型的优化计算和渲染着色器库，可以利用每个GPU的独特硬件。

// TODO: 暂时还不清楚SpriteKit、SceneKit与Metal之间的关系。

### 示例

[示例一：使用 Metal 查找 GPU 并对其执行计算](https://developer.apple.com/documentation/metal/performing_calculations_on_a_gpu)

[示例二：创建一个 MetalKit 视图和一个渲染通道来绘制视图的内容](https://developer.apple.com/documentation/metal/using_metal_to_draw_a_view_s_contents)

[示例三：渲染一个简单的 2D 三角形](https://developer.apple.com/documentation/metal/using_a_render_pipeline_to_render_primitives)





## 其他参考文档

https://juejin.cn/post/6844903619339223048

https://blog.csdn.net/Philm_iOS/article/details/81004596
