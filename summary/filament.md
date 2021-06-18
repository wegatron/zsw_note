## filament 源码分析

### filament overview

filament整体架构:
<figure class="image">
<center>
<img src="../rc/filament_structure.svg" width=700>
</center>
<center><em>filament代码头文件</em></center>
</figure>

* Tools主要是材质或模型的处理优化工具. 比如: 材质编译工具`matc`, 材质编辑器`tungsten`(未有成熟的Release).
* Frame Graph
    渲染帧的配置
* Rendering Data Abstract
    不同层次渲染数据以及其管理的抽象.
    * Rendering API Object抽象, 例如: Texture, Buffer, SwapChain, RenderTarget
    * 其他渲染管理数据的抽象, 如: Light, LightManager, Camera, Viewport, Material ... / Scene, Engine
* Material 材质的定义和解析

除了此部分之外, 对于Android, 还有特定的JNI导出`android/filament-android`.

### 主体库 filament

不同层次渲染数据以及其管理的抽象. 

<figure class="image">
<center>
<img src="../rc/filament_source_structure.png" width=200>
<img src="../rc/filament_source_structure_src_detail.png" width=200>
</center>
<center><em>filament代码头文件(左), filament代码src/detail(右)</em></center>
</figure>

在filament代码中使将类的实现细节和其开放的功能接口强分离, 每一个类开放的类都有一个加前缀F的实现类. 如filament主体库中这些实现类头文件在details目录下. 通过`upcast`引用实现类的实现, 如camera类中:

```c++
void Camera::setProjection(double fovInDegrees, double aspect, double near, double far,
        Camera::Fov direction) noexcept {
    upcast(this)->setProjection(fovInDegrees, aspect, near, far, direction);
}
```

`upcast`的定义:

```c++
#define FILAMENT_UPCAST(CLASS)                                      \
    inline F##CLASS& upcast(CLASS& that) noexcept {                 \
        return static_cast<F##CLASS &>(that);                       \
    }                                                               \
    inline const F##CLASS& upcast(const CLASS& that) noexcept {     \
        return static_cast<const F##CLASS &>(that);                 \
    }                                                               \
    inline F##CLASS* upcast(CLASS* that) noexcept {                 \
        return static_cast<F##CLASS *>(that);                       \
    }                                                               \
    inline F##CLASS const* upcast(CLASS const* that) noexcept {     \
        return static_cast<F##CLASS const *>(that);                 \
    }
```


#### 细说Material

官方文档第4小节, 专门介绍, 材料的文件格式`.filament`. JSON类似文件格式, 允许c++注释+key没有双引号. 包含`material`, `vertex`, `fragment`三部分.
在写filament中, 还定义了一些额外常用的API, 如`getTime()`见文档4.5节.

一个例子:

```json
material {
    name : "Textured material",
    parameters : [ //这里表示input uniform参数
        {
           type : sampler2d,
           name : texture
        },
        {
           type : float,
           name : metallic
        },
        {
            type : float,
            name : roughness
        }
    ],
    requires : [
        uv0
    ],
    shadingModel : lit, // 光照模式
    blending : opaque // 混合模式
}

fragment {
    void material(inout MaterialInputs material) {
        prepareMaterial(material); // 初始化 4.4.1
        material.baseColor = texture(materialParams_texture, getUV0());
        material.metallic = materialParams.metallic;
        material.roughness = materialParams.roughness;
    }
}
```

`matc`工具用来编译和验证`.filament`材质文件, 输出`.mat`文件. `matc`的一些参数:

| Flag | Value | Usage |
| ---- | ---- | ---- |
| __-o, --output__ | [path] | Specify the output file path |
| __-p, --platform__ | desktop/mobile/all | Select the target platform(s) |
| __-a, --api__ | opengl/vulkan/metal/all | Specify the target graphics API |
| __-S, --optimize-size__ | N/A | 不仅做性能优化, 且做文件大小优化 |
| __-r, --reflect__ | parameters | 将参数解析出来以JSON格式打印输出 |

Material在编译之后, 其实是成为了一个二进制的键值对. 在解析时`MaterialParser`通过库`filflat`, 来解析出数据. 根据解析的数据, 构建`FMaterial`.

```c++
class FMaterial : public Material {
    FMaterial(FEngine& engine, const Material::Builder& builder); // 参数的设置
    backend::Handle<backend::HwProgram> getProgram(uint8_t variantKey) const noexcept; // shader
}
```

__TODO Question: 对于有多个pass的material如何处理?__

### Backend

backend包括两部分:

* 图形渲染API抽象

* 平台窗口系统中间层抽象
    图形渲染API与本地窗口系统之间的中间层接口. 如: EGL(android), Cocoa(ios).

<figure class="image">
<center>
<img src="../rc/finament_backend.JPG" width=200>
</center>
<center><em>opengl(es) backend</em></center>
</figure>

平台窗口系统中间抽象, 在各个platform*.*中定义和实现. 可以看到, vulkan和metal这两种先进的框架, platform定义很简单, 主要就是创建driver对象. 而opengl则实现了一些其他必要的函数(如commit, makeCurrent, createSwapChain等).

#### backend初始化过程

在filament中, 大部分操作都需要用到`Engine`, 调用其相关函数, 但仔细看代码可以发现, `Engine`类其实只是构建了虚拟的资源, 并不真正干活.

```c++
template <typename T>
inline T* FEngine::create(ResourceList<T>& list, typename T::Builder const& builder) noexcept {
    T* p = mHeapAllocator.make<T>(*this, builder);
    list.insert(p);
    return p;
}
```

在filament中, `opengl context`, `vulkan/metal device`是怎么创建的?

在platform中封装了各个平台+窗口系统下, 各个图形API Driver的创建和销毁. 其中, Opengl由于其设计思想比较古老, 还添加了swapchain以及makecurrent等函数.

<figure class="image">
<center>
<img src="../rc/filament_platform.svg" width=1000>
</center>
<center><em>platform</em></center>
</figure>

几个重要的platform:
* PlatformWGL中, platform在创建的时候直接创建窗口, 并得到窗口的opengl context.

* PlatformVkWindows

* PlatformEGLAndroid

* PlatformVkAndroid

* platformMetal, 直接创建Device.

在创建了platform之后, 再使用OpenGLDriverFactory根据platform和context, 创建Driver.

#### backend资源创建过程



#### backend RenderPass组建过程

#### 其他Q&A:

TODO filament如何编译android/ios版本?
在cmake时使用特定的toolchain. android模拟器

~~如何区分OpenGL和OpenGLES的?~~



### JobSystem


### 内存管理

为什么不用c++默认的内存管理方式? $\to$ [游戏引擎开发新感觉！(6) c++17内存管理](https://zhuanlan.zhihu.com/p/96089089)

Memory Areana, 一块巨大连续的内存, 申请一次, 多次使用(CPU, GPU均有此优化的思想).

> An arena is just a large, contiguous piece of memory that you allocate once and then use to manage memory manually by handing out parts of that memory. 

__new operator和operator new__

new operator类似于`malloc`用来申请内存, 可以被重载. 而operator new, 除了申请内存外, 还执行类对象的构造函数.

```c++
// file allocators.h
#ifndef NDEBUG

// on Debug builds, HeapAllocatorArena needs LockingPolicy::Mutex because it uses a
// TrackingPolicy, which needs to be synchronized.
using HeapAllocatorArena = utils::Arena<
        utils::HeapAllocator,
        utils::LockingPolicy::Mutex,
        utils::TrackingPolicy::DebugAndHighWatermark>;

using LinearAllocatorArena = utils::Arena<
        utils::LinearAllocator,
        utils::LockingPolicy::NoLock,
        utils::TrackingPolicy::DebugAndHighWatermark>;

#else

// on Release builds, HeapAllocatorArena doesn't need a LockingPolicy because HeapAllocator is
// intrinsically synchronized as it relies on heap allocations (i.e.: malloc/free)
using HeapAllocatorArena = utils::Arena<
        utils::HeapAllocator,
        utils::LockingPolicy::NoLock>;

using LinearAllocatorArena = utils::Arena<
        utils::LinearAllocator,
        utils::LockingPolicy::NoLock>;

#endif
```

定义了3中allocator:

* `LinearAllocator` 比如command
    * 申请的内存地址线性递增. (在初始化时申请了一大块内存buffer).
    * 类似栈的方式(内存只能从顶部开始释放), 后申请的内存要先被释放.
    
* `HeapAllocator`
    使用系统的malloc和free, 在堆上申请和释放内存.

* `PoolAllocator`.
    用来分配小于指定大小的内存块. 允许无序释放.

⚠️ allocator在释放内存时, 不执行析构函数. 如何防止内存泄漏?

Trivial destructor
The destructor for class T is trivial if all of the following is true:

* The destructor is not user-provided (meaning, it is either implicitly declared, or explicitly defined as defaulted on its first declaration)
* The destructor is not virtual (that is, the base class destructor is not virtual)
* All direct base classes have trivial destructors
* All non-static data members of class type (or array of class type) have trivial destructors

A trivial destructor is a destructor that performs no action. Objects with trivial destructors don't require a delete-expression and may be disposed of by simply deallocating their storage. All data types compatible with the C language (POD types) are trivially destructible.

在ArenaScope于析构函数不是trival destructor的类需要执行析构函数.

```c++
// std::is_trivially_destructible<T>::value
if (std::is_trivially_destructible<T>::value) {
    o = mArena.template make<T, ALIGN>(std::forward<ARGS>(args)...);
    } else {
        void* const p = (Finalizer*)mArena.alloc(sizeof(T), ALIGN, sizeof(Finalizer));
        if (p != nullptr) {
            o = new(p) T(std::forward<ARGS>(args)...);
            f->finalizer = &destruct<T>;
            f->next = mFinalizerHead;
            mFinalizerHead = f;
        }
    }
}
```

在Arena中则通过`std::enable_if`不允许申请:
```c++
template <typename T,
    typename = typename std::enable_if<std::is_trivially_destructible<T>::value>::type>
    T* alloc(size_t count, size_t alignment = alignof(T), size_t extra = 0) { ... }
```

在申请内存时, 默认使用最大的scalar的大小(`alignof`是一个POD中最大的那个内存及其大小):

```c++
void* alloc(size_t size, size_t alignment = alignof(std::max_align_t), size_t extra = 0) {
    // this allocator doesn't support 'extra'
    assert(extra == 0);
    return aligned_alloc(size, alignment);
}
```

### FrameGraph


### 其他

#### 异常管理机制

#### 版本管理
Filament使用三个数字来定义一个版本:
* __most significant__ number, 当API不再向后兼容, 或者引入一套新的API.

* __middle number__, 当材质系统不再向后兼容. 此时需要对材质文件进行重新编译.
    
    另外, 在`MaterialEnums.h`文件中, 定义了`MATERIAL_VERSION`, 运行时检测材质版本是否对应, 需要同步更新.

* __least significant__, 每次Release版本更新时的版本号, 其他两个更新时重置为0.

值得借鉴的有两点:

* material 的版本管理

* material 文件定义了一个人类友好的格式(易读和理解), 在compile来转变为机器友好的格式(高效加载, 以及渲染优化).

#### 混合和半透明


## 参考资料
[filament Material]()
[游戏引擎开发新感觉！(6) c++17内存管理](https://zhuanlan.zhihu.com/p/96089089)
[《STL源码剖析》提炼总结：空间配置器(allocator)](https://zhuanlan.zhihu.com/p/34725232)