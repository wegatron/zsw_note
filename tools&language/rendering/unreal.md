## RDG Basic Concept
RDG全称是Rendering Dependency Graph，意为渲染依赖性图表，渲染子系统，基于有向无环图(Directed Acyclic Graph，DAG)的调度系统，用于执行渲染管线的整帧优化。
利用现代的图形API（DirectX 12、Vulkan和Metal 2），实现自动异步计算调度以及更高效的内存管理和屏障管理来提升性能.

先收集所有需要渲染的Pass，然后按照依赖的顺序对图表进行编译和执行，期间会执行各类裁剪和优化.

* 执行异步计算通道的自动调度和隔离。
* 在帧的不相交间隔期间，使资源之间的别名内存保持活跃状态。
* 尽早启动屏障和布局转换，避免管线延迟。

将引擎的各类渲染功能（Feature）和上层渲染逻辑（Renderer）和下层资源（Shader、RenderContext、图形API等）隔离开来，以便做进一步的解耦、优化，其中最重要的就是多线程和并行渲染。

UE代码路径: `UnrealEngine/Engine/Source/Runtime/RenderCore/Public`

### Design
Shader Parameter和过程分离
```c++

```

## 一些需要关注的问题
* Resource Tracking/Manage, 资源别名和重复利用
* 如何计算资源的生命周期, 怎么区分临时资源非临时资源
* 资源之间的相互依赖关系

## RDG




## Reference
* [【翻译】UE4 RDG系统速成课](https://zhuanlan.zhihu.com/p/374224919)
* [剖析虚幻渲染体系](https://www.cnblogs.com/timlly/p/15217090.html#1122-rdg%E8%B5%84%E6%BA%90)
* [虚幻引擎之Rendering Dependency Graph（一）](https://blog.csdn.net/qjh5606/article/details/118246059)
* [UE4RenderingDependencyGraph](https://papalqi.cn/ue4renderingdependencygraph/)