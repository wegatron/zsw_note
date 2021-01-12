## Render Engine
### Texture
Texture: 负责创建纹理对象, output texture的opengl handler
Shader Desc: sampler, 创建纹理sampler. output: <name, location>
Render: 在使用时, active TEXTUREi, 并设置sampler的值, 并绑定纹理对象.