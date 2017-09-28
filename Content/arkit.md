# ARKit

## 概述

1. 创建工程，选择 `Augmented Reality APP` 项目。

2. `ARKit`提供两种AR技术，一种基于3D场景(`SceneKit`），一种是基于2D场景(`SpriteKit`)。

3. `ARKit`：相机捕捉现实世界图像；`SceneKit`：在图像中显示虚拟3D模型。

4. `ARKit`中的增强显示视图`ARSCNView`是继承自`SceneKit`中的`SCNView`，`SCNView`是继承自`UIKit`中的`UIView`。

5. `ARSCNView`只是视图容器，作用在于管理`ARSession`；`ARCamera`负责捕捉摄像头画面，`ARSession`负责搭建3D场景。

6. 每个虚拟物体都是一个节点`SCNNode`，每个节点构成一个场景`SCNScene`，无数个场景构成了3D世界。


## ARKit类

1. `ARConfiguration` 与 `ARWorldTrackingConfiguration`

    * 作用是跟踪设备的方向和位置，以及检测设备摄像头看到的现实世界的表面。它的内部实现了一系列非常庞大的算法计算以及调用了你的iPhone必要的传感器来检测手机的移动及旋转甚至是翻滚。
    
    * 当`ARWorldTrackingConfiguration`计算出相机在3D世界中的位置时，它本身并不持有这个位置数据，而是将其计算出的位置数据交给`ARSession`去管理（与前面说的session管理内存相呼应），而相机的位置数据对应的类就是`ARFrame`(currentFrame)。
    
2. `ARAnchor` 与 `ARFrame`

   * `ARAnchor` 表示一个虚拟物体在3D空间的位置和方向。

   * `ARFrame` 表示的是AR相机的位置和方向以及追踪相机的时间，还有图像帧和时间等参数。

3. `ARCamera` 是一个相机，它是连接虚拟场景与现实场景之间的枢纽。在ARKit中，它是捕捉现实图像的相机，在SceneKit中它又是3D虚拟世界中的相机。（一般第一人称3D游戏，主角其实就是一个3D相机，我们电脑屏幕看到的画面就是这个相机捕捉的画面）。

4. `ARError`  是一个描述ARKit错误的类，这个错误来源于几个方面，例如设备不支持，或者当相机常驻后台时ARSession会断开等问题。

5. `ARHitTestResult` 点击回调结果，这个类主要用于虚拟增强现实技术（AR技术）中现实世界与3D场景中虚拟物体的交互。 比如我们在相机中移动。拖拽3D虚拟物体，都可以通过这个类来获取ARKit所捕捉的结果。

6. `ARLightEstimate`  是一个灯光效果，它可以让你的AR场景看起来更加的好。

7. `ARPlaneAnchor` 平地锚点，ARKit能够自动识别平地，并且会默认添加一个锚点到场景中，当然要想看到真实世界中的平地效果，需要我们自己使用SCNNode来渲染这个锚点。

8. `ARPointCloud` 点状渲染云，主要用于渲染场景。

9. `ARSCNView`  ARKit框架中用于3D显示的预览视图。

10. `ARSession` ARSession是一个连接底层与AR视图之间的桥梁，其实ARSCNView内部所有的代理方法都是由ARSession来提供的。

