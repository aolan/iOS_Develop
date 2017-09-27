# ARKit

1. 创建工程，选择 `Augmented Reality APP` 项目。

2. ARKit提供两种AR技术，一种基于3D场景(SceneKit），一种是基于2D场景(SpriteKit)。

3. ARKit：相机捕捉现实世界图像；SceneKit：在图像中显示虚拟3D模型。

4. ARKit中的增强显示视图ARSCNView是继承自SceneKit中的SCNView，SCNView是继承自UIKit中的UIView。

5. ARSCNView只是视图容器，作用在于管理ARSession；ARCamera负责捕捉摄像头画面，ARSession负责搭建3D场景。

6. 每个虚拟物体都是一个节点SCNNode，每个节点构成一个场景SCNScene，无数个场景构成了3D世界。
