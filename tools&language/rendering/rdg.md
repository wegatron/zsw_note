# Render Dependency Graph

## Features

* 自动添加fences等同步命令, 高效简单的实现异步

  Scheduling of asynchronous compute fences

* 通过跟踪临时资源的生命周期和别名, 更好高效地利用内存

  Allocation of transient resources with optimal lifetimes and memory aliasing

* 细粒度的barrier提高并行度/降低等待时延

  Transitioning of sub-resources using split-barriers to hide latency and improve overlap on the GPU

* 并行录制命令

  Parallel command list recording

* 裁剪无用资源和pass

  Culling of unused resources and passes in the graph

* 验证资源依赖和API使用

  Validation of API usage and resource dependencies

* 依赖关系可视化

  Visualization of the graph structure and memory lifetimes in RDG Insights

## UE Example

### Shader封装

Shader Inputs(HLSL):

```c++
float2 ViewportSize;
float4 Hello;
float World;
float3 FooBarArray[16];

Texture2D BlueNoiseTexture;
SamplerState BlueNoiseSampler;

Texture2D SceneColorTexture;
SamplerState SceneColorSampler;

RWTexture2D<float4> SceneColorOutput;
```



目标c++输入参数结构体:

```c++
struct FMyShaderParameters
{
    FVector2D ViewportSize;
    FVector4 Hello;
    float World;
    FVector FooBarArray[16];

    FRHITexture*        BlueNoiseTexture = nullptr;
    FRHISamplerState*   BlueNoiseSampler = nullptr;

    FRHITexture*        SceneColorTexture = nullptr;
    FRHISamplerState*   SceneColorSampler = nullptr;

    FRHIUnorderedAccessView* SceneColorOutput = nullptr;
};
```



使用宏来定义shader参数结构体(在编译期间自动生成目标结构体):

```c++
BEGIN_SHADER_PARAMETER_STRUCT(FMyShaderParameters, /** MODULE_API_TAG */)
    SHADER_PARAMETER(FVector2D, ViewportSize)
    SHADER_PARAMETER(FVector4, Hello)
    SHADER_PARAMETER(float, World)
    SHADER_PARAMETER_ARRAY(FVector, FooBarArray, [16])

    SHADER_PARAMETER_TEXTURE(Texture2D, BlueNoiseTexture)
    SHADER_PARAMETER_SAMPLER(SamplerState, BlueNoiseSampler)

    SHADER_PARAMETER_TEXTURE(Texture2D, SceneColorTexture)
    SHADER_PARAMETER_SAMPLER(SamplerState, SceneColorSampler)

    SHADER_PARAMETER_UAV(RWTexture2D, SceneColorOutput)
END_SHADER_PARAMETER_STRUCT()
```



这样写的目的是在编译阶段反射得到这个结构体的metadata, 从而实现与shader参数与c++结构体参数的自动对应+绑定:

```c++
class FMyShaderCS : public FGlobalShader
{
    DECLARE_GLOBAL_SHADER(FMyShaderCS);

    // Generates a constructor which will register FParameter bindings with this FShader instance.
    SHADER_USE_PARAMETER_STRUCT(FMyShaderCS, FGlobalShader);

    // Assign an FParameters type to the shader--either with an inline definition or using directive.
    using FParameters = FMyShaderParameters;
};
```



设置输入参数&使用:

```c++
TShaderMapRef<FMyShaderCS> ComputeShader(View.ShaderMap);
RHICmdList.SetComputeShader(ComputeShader.GetComputeShader());

FMyShaderCS::FParameters ShaderParameters;

// Assign the parameters.
ShaderParameters.ViewportSize = View.ViewRect.Size();
ShaderParameters.World = 1.0f;
ShaderParameters.FooBarArray[4] = FVector(1.0f, 0.5f, 0.5f);

// Submit the parameters.
SetShaderParameters(RHICmdList, ComputeShader, ComputeShader.GetComputeShader(), Parameters);

RHICmdList.DispatchComputeShader(GroupCount.X, GroupCount.Y, GroupCount.Z);
```



### Render Graph Builder

使用方式:

* 创建一个`FRDGBuilder` 实例, 往其中配置resource和pass. 调用`FRDGBuilder::Execute` 编译和运行.

* 通过`FRDGBuilder::CreateTexture`、`FRDGBuilder::CreateBuffer` 创建RDG resources(这些资源在RDG真正执行的时候才会被创建).
* 使用`FRDGBuilder::AddPass` 结合当前pass参数和一个excution lambda来创建(添加)一个pass
  * pass parameter struct中包含shader parameter以及RDG resources
    * RDG 通过这些参数来推导pass之间的依赖关系以及资源的lifetime
    * excution lambda中使用 `GraphBuilder::AllocParameters` 来为参数申请内存, 然后赋值.
  * graph执行的时候, 通过excution lambda将RHI command录制到RHI command list. <<并行录制如何体现?>>

样例代码:

```c++
{
    FRDGBuilder GraphBuilder(RHICmdList);

    FMyShaderCS::FParameters* PassParameters = GraphBuilder.AllocParameters<FMyShaderCS::FParameters>();
    //...
    PassParameters->SceneColorTexture = SceneColor;
    PassParameters->SceneColorSampler = TStaticSamplerState<SF_Point, AM_Clamp, AM_Clamp>::GetRHI();
    PassParameters->SceneColorOutput = GraphBuilder.CreateUAV(NewSceneColor);

    GraphBuilder.AddPass(
        // Friendly name of the pass for profilers using printf semantics.
        RDG_EVENT_NAME("MyShader %d%d", View.ViewRect.Width(), View.ViewRect.Height()),
        // Parameters provided to RDG.
        PassParameters,
        // Issues compute commands.
        ERDGPassFlags::Compute,
        // This is deferred until Execute. May execute in parallel with other passes.
        [PassParameters, ComputeShader, GroupCount] (FRHIComputeCommandList& RHICmdList)
    {
        FComputeShaderUtils::Dispatch(RHICmdList, ComputeShader, PassParameters, GroupCount);
    });

    // Execute the graph.
    GraphBuilder.Execute();
}
```



数据更新在哪里???

## Reference

[UE5.0 RDG Programming Guide](https://docs.unrealengine.com/5.0/en-US/render-dependency-graph-in-unreal-engine/)

[UE RDG 源码解析](https://www.cnblogs.com/timlly/p/15217090.html)

