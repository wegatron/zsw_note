## SPIR-V
一种中间二进制码, 可以通过高层语言如glsl, hlsl生成.
> SPIR-Visa Khronos-standard intermediatelanguagethat providesan alternativefor distributing shaders.

SPIR-V以二进制模块为单位, 一个模块包含一个或多个函数, 构成一个独立的pipline stage, 比如vertex shader.

## 相关工具

### glslangValidator
官方提供的glsl shading language前端, 完成: 词法分析、语法分析、语义分析、中间代码产生(AST).
> An OpenGL GLSL and OpenGL|ES GLSL (ESSL) front-end for reference validation and translation of GLSL/ESSL into an internal abstract syntax tree (AST).

支持的glsl版本: https://www.khronos.org/opengles/sdk/tools/Reference-Compiler/

同时也支持hsls编译生成spir-v抽象语法树(AST).

glslangValidator可以包含spirv-tools, 来保证通过hlsl生成的spir-v能够在vulkan上是合法的且大小尽可能小.
> If you wish to assure that SPIR-V generated from HLSL is legal for Vulkan, wish to invoke -Os to reduce SPIR-V size from HLSL or GLSL, or wish to run the integrated test suite, install spirv-tools with this:

*以命令行形式使用.*

glslangValidator


### SPIRV-Cross
将spir-v转换为其他类型的语言, 包括: 指定版本的glsl(glsl es), msl, 

*主要以代码API形式使用, CLI只提供简单功能.*

### spirv-tools
一些其他重要的功能, 比如: spir-v二进制的解析, 验证, 优化等.
>The SPIR-V Tools project provides an API and commands for processing SPIR-V modules.

*CLI和代码API形式使用*

## glsl es
一些常用的标记
`layout`
`location`
`in`, `out`

## Reference
[]()