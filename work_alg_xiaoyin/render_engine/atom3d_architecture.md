# Atom3d Architecture
## code structure
一些重要的代码文件及其介绍:

```
atom3d
├── component #渲染时的一些基本组件
│   ├── component.* #组件基类, 可通过之获取所属scene,system
│   ├── animation.* #控制动画状态, 播放时调用具体的animationTrack更新参数状态
│   ├── camera.* #相机矩阵的计算
│   ├── light.* #灯光, 包括类型和参数
│   ├── mesh_render.* #加入了网格在绘制时的一些设置参数, 当前用处不大
│   └── ...
├── glTF #glTF文件的解析和加载
│   ├── gltf_loader.*
│   ├── gltf2_loader.* 
│   └── ...
├── kernel
│   ├── atom3d_effect.*
│   ├── atom3d_effect_{platform}.*
│   ├── atom3d_engine.*
│   ├── inner_information.*
│   ├── res_identitier.*
│   ├── res_loader.*
│   ├── system3d.*
│   ├── xml_dom.*
│   └── ...
├── math
│   ├── aabbox.*
│   ├── color.*
│   ├── fft.*
│   ├── frustum.*
│   ├── quaternion.*
│   ├── transform.*
│   ├── ray.*
│   ├── plane.*
│   └── ...
├── media
├── rendering
│   ├── aabbox.*
│   ├── color.*
│   ├── animation_track.* #关键帧动画数据(TransformAnimationTrack, WeightsAnimationTrack)以及对应的scene_obj
│   ├── rendable.* #材质, 灯光, 以及绘制的一些配置
│   ├── render_technique.* #effect的一种实现方案. RenderTechnique, RenderPass
│   ├── render_effect.* #某种绘制的多个实现方案. 原来是3d渲染时, 有不同绘制技术: 如是否绘制皮肤, 需要使用不同的shader和参数
│   ├── postprocess.* #定义了一个后处理流程, 可以由多个effect构成
│   ├── render_engine.* #整体绘制的设置, 包括FBO上depth和color的挂载, y是否需要filp
│   ├── render_factory.* #创建渲染绘制对象: texture, buffer, RenderView(输出目标的封装)
│   ├── render_helper.* #提供设置数据的接口
│   ├── rendering_layer.* #???
│   ├── shader_object.* #
├── rendering_gles
├── rendering_metal
├── rendering_scenekit
├── scene
└── utils
```

一些
1. 

