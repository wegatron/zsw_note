## Tile-Based Deferred Rendering
两种渲染架构的GPU: Immediate Mode Rendering, Tile-Based Deferred Rendering.
Tile-Based Deferred Rendering的优势:
* It drastically saves on memory bandwidth because of the unified memory architecture.
* Blending happens in-register facilitated by tile processing.
* Color, depth and stencil buffers don’t need to be re-fetched.

🍒@TODO read Opengl Insight ch23 in books

## Reference
[Tile-Based Defereed Rendering](https://zhuanlan.zhihu.com/p/26279464)
[New in Metal and the Apple GPU](http://metalkit.org/2020/07/03/wwdc20-whats-new-in-metal.html)