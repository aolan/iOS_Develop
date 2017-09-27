# ARKit

1. 创建工程，选择 `Augmented Reality APP` 项目。

2. `ARKit`提供两种AR技术，一种基于3D场景(`SceneKit`），一种是基于2D场景(`SpriteKit`)。

3. `ARKit`：相机捕捉现实世界图像；`SceneKit`：在图像中显示虚拟3D模型。

4. `ARKit`中的增强显示视图`ARSCNView`是继承自`SceneKit`中的`SCNView`，`SCNView`是继承自`UIKit`中的`UIView`。

5. `ARSCNView`只是视图容器，作用在于管理`ARSession`；`ARCamera`负责捕捉摄像头画面，`ARSession`负责搭建3D场景。

6. 每个虚拟物体都是一个节点`SCNNode`，每个节点构成一个场景`SCNScene`，无数个场景构成了3D世界。

7. `ARWorldTrackingSessionConfiguration`

    * 作用是跟踪设备的方向和位置，以及检测设备摄像头看到的现实世界的表面。它的内部实现了一系列非常庞大的算法计算以及调用了你的iPhone必要的传感器来检测手机的移动及旋转甚至是翻滚。
    
    * 当`ARWorldTrackingSessionConfiguration`计算出相机在3D世界中的位置时，它本身并不持有这个位置数据，而是将其计算出的位置数据交给`ARSession`去管理（与前面说的session管理内存相呼应），而相机的位置数据对应的类就是`ARFrame`(currentFrame)。
