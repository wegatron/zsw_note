## 目标
设计师给定一系列keyframe的欧拉角, 找到一个光滑的旋转曲线(路径), 将这些keyframe连接起来.


## 旋转的表示
欧拉角: 直观易理解, 任意尺度的旋转; 万向锁问题以及插值后不够顺滑.
四元素: 状态和值一一对应, 插值顺滑; 不够直观, 无法表示超过180度的旋转.

## unity 的做法
使用欧拉角作为UI, 并对keyframe进行重采样转换到四元素, 可以选择使用欧拉角还是四元素.


## Affine transformation decomposition

https://math.stackexchange.com/questions/2904766/decomposition-of-a-linear-map-into-scale-rotation-shear-matrices

https://math.stackexchange.com/questions/612006/decomposing-an-affine-transformation

## 参考资料
Unity:

https://docs.unity3d.com/2019.2/Documentation/Manual/AnimationEulerCurveImport.html

https://github.com/Unity-Technologies/UnityCsReference/blob/master/Editor/Mono/Animation/AnimationWindow/RotationCurveInterpolation.cs

https://github.com/Unity-Technologies/UnityCsReference/blob/9034442437e6b5efe28c51d02e978a96a3ce5439/Editor/Mono/Animation/AnimationWindow/CurveRenderer/EulerCurveCombinedRenderer.cs

https://github.com/Unity-Technologies/UnityCsReference/tree/9034442437e6b5efe28c51d02e978a96a3ce5439/Editor/Mono/Animation/AnimationWindow/CurveRenderer


插值:

http://roboticslab.snu.ac.kr/fcp/files/_pdf_files_publications/5_mdc/smooth_invariant_interpolation.pdf
