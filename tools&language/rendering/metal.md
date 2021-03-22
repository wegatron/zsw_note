## Metal 相关资料
[🍇 The Metal Framework](http://metalkit.org/)
[🍋 Metal学习——基础概念与框架](https://hello-david.github.io/archives/98dc9fea.html)
[快速学习Metal API](https://www.zhihu.com/question/29070544)
[Metal2 研发笔录](https://zhuanlan.zhihu.com/c_1180576107602034688)
[小demo](https://www.jianshu.com/u/f141ef15ba07)
[Metal By Example](https://metalbyexample.com/the-book/)
[Metal By Tutorial](https://www.raywenderlich.com/books/metal-by-tutorials/v2.0/chapters/11-tessellation-terrains#toc-chapter-014-anchor-007)
[资深程序员的Metal入门教程总结](https://www.cnblogs.com/qcloud1001/p/9890961.html)

## 使用Metal kernel function 实现图像滤波
[Metal Filter](https://metalbyexample.com/fundamentals-of-image-processing)
[metal kernel function简单实例](https://avinashselvam.medium.com/hands-on-metal-image-processing-using-apples-gpu-framework-8e5306172765)
[MetalPetal kernel function Filters library](https://github.com/MetalPetal/MetalPetal/blob/master/Frameworks/MetalPetal/Kernels/MTIMPSKernel.m)
[🍒 Metal - 11 GPGPU 通用计算（Compute Shader）](https://www.uiimage.com/post/blog/metal/metal-11-generic-purpose-computing/)
[编译metallib](https://developer.apple.com/documentation/metal/libraries/building_a_library_with_metal_s_command-line_tools?language=objc)

## Metal的一些问题

1. 在metal中当纹理的存储数据type与纹理在mtlfunction中使用的type不完全一致时, 而外的开销有多大?
    比如新建纹理时所用的pixel format为:`MTLPixelFormatRGBA8Unorm`, 而在mtlfunction使用时可以为`texture2d<half, access::read>`或者`depth2d<float, access::read>`. 哪种方式效率更快?

2. 在texture2d不支持uint8为什么?
    参考《Metal Shading Language Specification》
    >For texture types (except depth texture types), T can be half, float, short, ushort, int, or uint. For depth texture types, T must be float.

3. 深度纹理不支持写入, 为什么?
    ```c++
    kernel void average_blur(depth2d<float, access::read> input [[texture(0)]],
                          depth2d<float, access::read_write> output [[texture(1)]],
                          uint2 id [[thread_position_in_grid]]);
    ```
    报错:
    ```
    "Depth textures must have access qualifier access::read or access::sample"
    ```