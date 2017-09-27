# ARKit

1. 创建工程，选择 `Augmented Reality APP` 项目。

2. `ARKit`提供两种AR技术，一种基于3D场景(`SceneKit`），一种是基于2D场景(`SpriteKit`)。

3. `ARKit`：相机捕捉现实世界图像；`SceneKit`：在图像中显示虚拟3D模型。

4. `ARKit`中的增强显示视图`ARSCNView`是继承自`SceneKit`中的`SCNView`，`SCNView`是继承自`UIKit`中的`UIView`。

5. `ARSCNView`只是视图容器，作用在于管理`ARSession`；`ARCamera`负责捕捉摄像头画面，`ARSession`负责搭建3D场景。

6. 每个虚拟物体都是一个节点`SCNNode`，每个节点构成一个场景`SCNScene`，无数个场景构成了3D世界。
