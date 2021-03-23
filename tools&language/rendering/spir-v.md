## SPIR-V
一种中间二进制码, 可以通过高层语言如glsl, hlsl生成.
> SPIR-Visa Khronos-standard intermediatelanguagethat providesan alternativefor distributing shaders.

SPIR-V以二进制模块为单位, 一个模块包含一个或多个函数, 构成一个单独的pipline stage, 无法相互链接.

官方编译器: `glslangvalidator`.

## glsl es
一些常用的标记
`layout`
`location`
`in`, `out`
