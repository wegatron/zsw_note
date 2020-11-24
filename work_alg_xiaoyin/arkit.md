## Target
1. 抠像, occlusion
2. 自拍效果，您可以在脸部网格上渲染半透明的纹理，以实现虚拟纹身或脸部涂料等效果，或者进行化妆，留胡须或胡须，或者用珠宝，面具遮盖网
格， 帽子和眼镜。
3. 面部捕捉，您可以实时捕捉面部表情并将其用作将表情投影到虚拟角色或游戏中角色的装备。


## Apple Arkit
1. scanning & reconstruction (point cloud, mesh)
2. sematic information
    * classification
    * motion capture
    * tracking
    * people occlution

## Apple Realitykit
RealityKit provides photo-realistic rendering, camera effects, animations, physics, and a lot more. It was built from the ground up specifically for AR.

1. physics interaction between virtual objects and real ones
2. rendering
    * occlusion
    * real and virtual illumination both real and unreal object
3. Face tracking, Location anchor

## 技术点
1. And ARKit uses your face as a light probe to estimate lighting conditions, and generates spherical harmonics coefficients that you can apply to your rendering.

2. We have a new property on ARConfiguration called FrameSemantics.
This will give you different kinds of semantic information of what's in the current frame.
You can also check if a certain semantic is available on the specific configuration or device with an additional method on the ARConfiguration.
Specific for people occlusion, there are two methods available that you can use. One option is person segmentation.
This will-- you provide just with the segmentation of people rendered on top of the camera image.
That's the best choice if you know that people will always be standing upfront and your virtual content will always be behind those people.

## 参数
Lidar 60HZ
Front depth image 15HZ