# Unity

unity manual: https://docs.unity3d.com/Manual/GraphicsAPIs.html

## Visual Effect Graph
### VFX失败(unity bug?)
https://issuetracker.unity3d.com/issues/vfx-opengles3-error-when-compiling-shaders-unexpected-token-number-at-kernel-csmain
>For now this bug can be fixed by going to:
>C:\Program Files\Unity\Hub\Editor\2022.1.0b6\Editor\Data\Resources\PackageManager\BuiltInPackages\com.unity.visualeffectgraph\Shaders\VFXCommon.hlsl
>
>Then go and change CUBEARRAY to 0 instead of 1.
>#ifdef SHADER_STAGE_COMPUTE
>#define SHADER_AVAILABLE_CUBEARRAY 0
>#endif

>I still have this in (at the time of writing newest version (2022.1.0b6)
>Also in 2022.1.0b4.
>It prevents building for Android.
>No build is necessary to observe the issue.
>The issue is in: VFXCommon.hlsl line: 136

### VFX粒子系统重要参数的解释
官方粒子系统的解析: https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@10.2/manual/Systems.html
比较好的解释的blog: https://iamsleepingnow.github.io/2021/03/03/Unity/Unity%E5%A4%A9%E5%9D%91VFX/