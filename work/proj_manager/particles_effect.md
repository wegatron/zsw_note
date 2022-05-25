## 第一阶段
参考使用unity vfx来实现粒子效果demo.

设想目标: 实现对视频中的人物添加立体的粒子效果

1. 理解smrvfx, 3d人物模型生成粒子效果
    粒子的发射和演变: 主要在vfx graph之中, 有待分析其实现

    记录：
        完成visual effect graph基本框架熟悉
        开始分析smrvfx demo, 下一步开始熟悉skined mesh
2. 考虑2D
    human person segmentation: https://developer.apple.com/documentation/vision/applying_matte_effects_to_people_in_images_and_video?language=objc
    a. 光流跟踪替代3d mesh上的point tracking?
    b. 使用关节点跟踪, 根据关节点连线, 进行粒子的发射
    mask 粒子发射和演变

3. demo

https://github.com/keijiro/jp.keijiro.beta
有意思的粒子效果: https://www.youtube.com/watch?v=bEn2--bKM0I
教程: https://www.bilibili.com/video/BV1SK411c7hm/

## 第二阶段
考虑3D, video -> 3d avatar, 
相关参考: 
https://github.com/rlczddl/awesome-3d-human-reconstruction
https://smpl.is.tue.mpg.de/