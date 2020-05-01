# 一些程序优化的策略
## 减少程序的分支
>Jumps/branches are expensive. Minimize their use whenever possible

## Cache的优化
* 将需要同时用到的数据和函数放到一起
将可能需要同时访问的数据打包到一起，比如顶点的Attribute: position, normal


## 考虑指令级别的并行
虽然程序可能是单线程的，但是不相关的指令可以是并行的.
> Even though many applications still rely on single threaded execution, modern CPUs already have a significant amount of parallelism inside a single core. This means a single CPU might be simultaneously executing 4 floating point multiplies, waiting for 4 memory requests, and performing a comparison for an upcoming branch.

## 变量的优化
* 减少局部变量，当局部变量很少的时候可以保存在寄存器中, 从而提升速度. (在for循环中，简单的局部变量如int速度比使用全局的要快, 无关性更好, 保存在寄存器中的话.)
* 减少函数参数

## 类型转换的开销
float, int 使用不同的寄存器, cast需要拷贝.
> Integer and floating point instructions often operate on different registers, so a cast requires a copy.
short 和 char 也同样需要一个通用大小的寄存器, 而且在保存时需要额外的开销.

## 合理利用查表
内存的寻址也很耗时，综合考虑是否查表

## 构造函数
* 简化默认构造函数
* 使用初始化列表
>Use constructor initializer lists. (Use Color::Color() : r(0), g(0), b(0) {} rather than Color::Color() { r
= g = b = 0; } .)

## 延后局部变量的定义
变量定义时一般需要调用构造函数, 被使用时才去定义, 可减少不必要的开销.

## 减少动态内存的申请
动态内存的申请通常需要调用锁,所以很影响并行的效率. 就算是单线程的程序,在堆上申请内存也比在栈上申请更慢(系统需要寻找一个合适大小的memory block)
>The process of dynamic allocation and deallocation of memory takes much more
time than other kinds of storage.

>There is no reason to use dynamic memory allocation
when the size of an array or the number of objects is known at compile time or a reasonable
upper limit can be defined.

## 使用memset初始化大块的内存

## 循环的优化
* 对于大部分复杂的循环, 考虑提前退出可以避免很多不必要的计算. 或者将一个循环拆分，以减少不必要的计算(如得分表优化的例子, 先选出最近点，后计算与最近点的关系). 对于简单的循环，添加退出分支可能会导致效率变低.

* 循环的展开
```c++
for(int i=0; i<100; ++i) { a[i] = b[i] + 1; }
// change to
for(int i=0; i<100; i+=4)
{
    a[i] = b[i] + 1;
    a[i+1] = b[i+1] + 1;
    a[i+2] = b[i+2] + 1;
    a[i+3] = b[i+3] + 1;
}
```

## 数学公式的优化
在纸上优化数学公式，避免不必要的计算或者重复计算.
* 乘法比除法快(如果多次使用除x,可以先计算1/x)，并使用乘法

## int, 32位浮点, 64位浮点的计算量差异并不大
>* On modern CPUs, floating-point operations have essentially the same throughput as integer operations.
In compute-intensive programs like ray tracing, this leads to a negligable difference between integer and
floating-point costs. This means, you should not go out of your way to use integer operations.
> * Double precision floating-point operations may not be slower than single precision floats, particularly on
64-bit machines. I have seen ray tracers run faster using all doubles than all floats on the same machine. I
have also seen the reverse

# 一些与程序performance相关的基本知识
## CISC和RISC平台的优劣
>The CISC instruction set may actually be better than RISC in situations where code caching is critical.

## 32位和64位程序的优劣
>The 64-bit systems can improve the performance by 5-10% for some CPU-intensive applications with many function calls.

64位程序的优势:
* 64位程序可用的寄存器数量比32多一倍
* 64位程序函数参数通过寄存器传递(32位程序通过栈传递)
* 64位程序在申请和释放大块内存时效率更高
* 64位可用SSE2指令集合

劣势:
* 指针使用64位表示, 影响cache的命中率
* 一些指令的长度更长

## 不同类型的存储
* 栈上(访存速度很快), 包含局部变量, 函数参数等
>If there are no big arrays, then it is almost certain that this part of the memory is mirrored in the level-1 data cache, where it is accessed quite fast.

* 全局和static
>The advantage of static data is that it can be initialized to desired values before the program
starts. The disadvantage is that the memory space is occupied throughout the whole
program execution, even if the variable is only used in a small part of the program. This
makes data caching less efficient.

合理减少全局变量的使用，对于多个函数需要访问同一个变量,可以将变量作为一个类的成员.

* 寄存器
浮点数和整型使用不同类型的寄存器.
>Floating point variables use a different kind of registers. There are eight floating point
registers available in 32-bit operating systems and sixteen in 64-bit operating systems.

* volatile
保证变量不被优化保存到寄存器中, 可用来测试保证有些b表达式被优化.
>The effect of the keyword volatile is that it makes sure the variable is stored in memory
rather than in a register and prevents all optimizations on the variable. This can be useful in
test situations to avoid that some expression is optimized away.

## 编译器对代码的优化
* 固定值得预计算
* 常见的子表达式消除
```c++
// Example 8.6a
int a, b, c;
b = (a+1) * (a+1);
c = (a+1) / 4;

// Example 8.6b
int a, b, c, temp;
temp = a+1;
b = temp * temp;
c = temp / 4;
```
* 将值保存在寄存器中
* 将相同的branch合并
```c++
// Example 8.8a
double x, y, z; bool b;
if (b) {
    y = sin(x);
    z = y + 1.;
}
else {
    y = cos(x);
    z = y + 1.;
}

// Example 8.8b
double x, y; bool b;
if (b) {
    y = sin(x);
}
```
* 跳转的消除
>This code has a jump from a=a*2; to return a+1;. The compiler can eliminate this jump
by copying the return statement.
```c++
// Example 8.9a
int SomeFunction (int a, bool b) {
if (b) {
    a = a * 2;
}
else {
    a = a * 3;
}
    return a + 1;
}

// Example 8.9b
int SomeFunction (int a, bool b) {
if (b) {
    a = a * 2;
    return a + 1;
}
else {
    a = a * 3;
    return a + 1;
}
}
```

* Loop的展开
有些编译器会在优化等级很高的时候将loop展开.
```c++
// Example 8.12a
int i, a[2];
for (i = 0; i < 2; i++) a[i] = i+1;

// Example 8.12b
int a[2];
a[0] = 1; a[1] = 2;
```

* 将与循环无关的代码移到循环外
```c++
// Example 8.13a
int i, a[100], b;
for (i = 0; i < 100; i++) {
a[i] = b * b + 1;
}

// Example 8.13b
int i, a[100], b, temp;
temp = b * b + 1;
for (i = 0; i < 100; i++) {
a[i] = temp;
}
```



# 参考文献
* https://people.cs.clemson.edu/~dhouse/courses/405/papers/optimize.pdf
* Optimizing software in C++ An optimization guide for Windows, Linux and Mac platforms(By Agner Fog. Technical University of Denmark)
