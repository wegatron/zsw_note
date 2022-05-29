## Advance Memory Management

### pmr

C++ 17引入了新的内存管理工具pmr(polymorphic memory resources). 内存管理分为两个部分: 内存资源(memory_resource), 分配器(allocator). 

```c++
namespace std::pmr{

    // 管理内存资源
    class memory_resource {
    public:
        virtual ~memory_resource();
    
        // 本身不是虚函数 , 调用虚函数实现
        void * allocate(size_t bytes, size_t alignment);
        void deallocate(void *p, size_t bytes, size_t alignment);

        // 用来判断从一个memory_resource申请的内存是否可以还回到另一个对象
        // 基本上是两个memory_resource是同一对象才返回true, 也有例外:
        // 使用的是全局默认, 或者是同一个资源的不同wrapper
        bool is_equal(const memory_resource& other) const noexecpt;

    private:
        virtual void* do_allocate( std::size_t bytes, std::size_t alignment ) = 0

        virtual void do_deallocate( void* p, std::size_t bytes, std::size_t alignment ) = 0;

        virtual bool do_is_equal( const std::pmr::memory_resource& other ) const noexcept = 0;   
    };


    // 为对象分配内存资源
    template<class Tp>
    class polymorphic_allocator
    {
    public:
        // 使用默认的内存资源 new delete
        polymorphic_allocator() noexcept;

        // 使用指定内存资源
        polymorphic_allocator(memory_resource * r) noexcept;
        memory_resource * resource() const;

        // 为对象分配内存资源
        Tp * allocate(size_t n);

        // 内存资源回收
        void deallocate(Tp * p, size_t n);
        ...
    };

}
```

#### 可直接使用的类

* `new_delete_resource()`: Allocates using ::operator new ▪ null_memory_resource(): Throws on allocation

* `synchronized_pool_resource`: Thread-safe pools of similarsized memory blocks.

* `unsynchronized_pool_resource`: Non-thread-safe pools of similar-sized memory blocks.

* `monotonic_buffer_resource`: Super-fast, non-thread-safe allocation into a buffer with do-nothing deallocation.
    向前分配, 2的指数递增.

* std::pmr::vector, std::pmr::string, ...

性能:

#### class using pmr

### Advance Memory Management

* 跟踪内存的申请、释放, 以及内存的高水位线(high-watermark).
* 确保deallocation与allocation一致.
* 内存泄漏 -  resource析构时, 任然有未释放的内存.

