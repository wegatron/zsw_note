## Target
1. 抠像, occlusion
    __front camera:__
    [Using AVFoundation’s AVCapturePhotoOutput, your app can find out whether a particular camera configuration supports the delivery of a portrait effect mattes to still images, and opt in to have them delivered on a per-photo request basis. AVCapturePhotoOutput delivers photo results using an in-memory wrapper object called AVCapturePhoto.](https://developer.apple.com/documentation/avfoundation/cameras_and_media_capture/enhancing_live_video_by_leveraging_truedepth_camera_data)

    __🍎rear camera with lidar:__
    [use segmentationBuffer and estimatedDepthData to implement people occlusion yourself.](https://developer.apple.com/documentation/arkit/arconfiguration/3089121-framesemantics)
    TODO Demo

2. 将人物移动到相同场景的同一个位置.
    [ARBodyAnchor](https://developer.apple.com/documentation/arkit/arbodytrackingconfiguration) + 场景scanning

3. 自拍效果，您可以在脸部网格上渲染半透明的纹理，以实现虚拟纹身或脸部涂料等效果，或者进行化妆，留胡须或胡须，或者用珠宝，面具遮盖网格， 帽子和眼镜。

4. 面部捕捉，您可以实时捕捉面部表情并将其用作将表情投影到虚拟角色或游戏中角色的装备。


## Apple Arkit
1. 🍋scanning & reconstruction (point cloud, mesh)
    demo
2. 🍋Location anchor
3. sematic information
    * 🍋classification
    * motion capture
    * tracking
    * 🍋people occlution
    >We have a new property on ARConfiguration called FrameSemantics.
    This will give you different kinds of semantic information of what's in the current frame.
    You can also check if a certain semantic is available on the specific configuration or device with an additional method on the ARConfiguration.
    Specific for people occlusion, there are two methods available that you can use. One option is person segmentation.
    This will-- you provide just with the segmentation of people rendered on top of the camera image.
    That's the best choice if you know that people will always be standing upfront and your virtual content will always be behind those people.
    

## Apple Realitykit
RealityKit provides photo-realistic rendering, camera effects, animations, physics, and a lot more. It was built from the ground up specifically for AR.

1. physics interaction between virtual objects and real ones
2. rendering
    * occlusion
    * real and virtual illumination both real and unreal object
    >And ARKit uses your face as a light probe to estimate lighting conditions, and generates spherical harmonics coefficients that you can apply to your rendering.
3. Face tracking, 


## 参数
Lidar 60HZ
Front depth image 15HZ

## app galary
https://github.com/olucurious/Awesome-ARKit