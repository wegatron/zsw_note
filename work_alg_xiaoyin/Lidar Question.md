## iOS Lidar 相关问题
1. [Improved Object Occlusion Rendering](https://developer.apple.com/augmented-reality/realitykit/)
    >Now, virtual objects can be placed under tables, behind walls, or around corners and you’ll see only the parts of the virtual object you’d expect to, with crisp definition of where the physical object hides part of the virtual one.

    ARKit + RealityKit 中有没有现实(包含动态的人和静态的背景)与虚拟物体的相互遮挡的预设方案(如果有, 需不需要做场景的预扫描, 还是即开即用).
    * 我们尝试了官网的Demo [Effecting People Occlusion in Custom Renderers](https://developer.apple.com/documentation/arkit/effecting_people_occlusion_in_custom_renderers), 该Demo将人与背景分离, 并得到了精度较高的人的深度信息, 但缺失了背景的深度信息.

    * 参考[Creating a Fog Effect Using Scene Depth](https://developer.apple.com/documentation/arkit/creating_a_fog_effect_using_scene_depth)这个Demo, 我们获取到的深度信息精度并不高, 有比较重的锯齿和边界抖动(帧与帧之间). ARKit + RealityKit 有没有既定的方案可以解决这两个问题.

2. 传感器数据的存储.
    Reality Composer可以录制传感器相关的数据, 然后在XCode上作为调试使用. 但我们没有找到公开的录制数据的API, 后续有没有计划提供这样的API接口.

3. RealityKit
    RealityKit提供了很多方便的AR接口, 比如物理模拟, 光照估计以及虚拟光源等. 这些功能是否必须结合ARView使用, 是否支持单独使用, 即在我们自己的Render是否可以使用这些功能? 如果可以, 能否提供一些相关资料, 或者这样的Demo.

