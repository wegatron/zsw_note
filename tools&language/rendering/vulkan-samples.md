# vulkan samples note
##  Texture Loading
### Common
...

swapchain
frame buffer

* prepare()
    * 创建swapchain以及其中的vkimage.
    * 创建semaphores.acquired_image_ready, 等待swapchain中获取下一个index的vkimage
    * 创建semaphores.render_complete, 等待渲染完成, 以进行present

* prepare_frame()
    从swapchain中获取下一个index的vkimage

* submit_frame()


### Load Texture

* load_texture()
    加载ktx纹理(支持不同种类的纹理结构2D texture, cube map array等. 以及不同种类的纹理压缩方式.), 但例子中没有用到压缩. 

    这里提到: __通常情况下图片保存的是SRGB__

    创建Image分为两种: GPU only(需要使用staging buffer进行拷贝, 可以将mipmap数据同时拷贝到vkimage中), 或是VK_MEMORY_PROPERTY_HOST_COHERENT_BIT, 可以直接拷贝. 这里使用的都是optimal_tiling, 也是直接拷贝原始图片数据(没有进行对齐之类的操作).

    在加载时加入了两个barrier, 一个用来等待cpu写数据到staging buffer, 另一个用来等待staging buffer将数据拷贝到vkimage.

* setup_descriptor_set_layout()
    设置sescriptor_set_layout, pipeline_layout.

* prepare_pipeline()
    各种state, 在vulkan中state可以是静态写死在pipeline_create_info中, 也可以是dynamic的, 在pipeline_create_info.pDynamicState标记.
    TODO, how to set dynamically?
    ```c++
	pipeline_create_info.pVertexInputState   = &vertex_input_state;
	pipeline_create_info.pInputAssemblyState = &input_assembly_state;
	pipeline_create_info.pRasterizationState = &rasterization_state;
	pipeline_create_info.pColorBlendState    = &color_blend_state;
	pipeline_create_info.pMultisampleState   = &multisample_state;
	pipeline_create_info.pViewportState      = &viewport_state;
	pipeline_create_info.pDepthStencilState  = &depth_stencil_state;
	pipeline_create_info.pDynamicState       = &dynamic_state;
	pipeline_create_info.stageCount          = static_cast<uint32_t>(shader_stages.size());
	pipeline_create_info.pStages             = shader_stages.data();    
    ```

* setup_descriptor_pool() && setup_descriptor_set_layout()
    descriptor_pool是一个池子, 里边定义了不同种Descriptor以及其可用的count. 例如, 一个pool中可能包含1个uniform buffer descriptor和一个combined image sampler descriptor.
    通过, vkAllocateDescriptorSets来从pool中申请一个descriptor_set, 再通过vkUpdateDescriptorSets(VkWriteDescriptorSet)进行更新.

* build_command_buffers()
    ```c++
    VkRenderPassBeginInfo render_pass_begin_info    = vkb::initializers::render_pass_begin_info();
	render_pass_begin_info.renderPass               = render_pass;
	render_pass_begin_info.renderArea.offset.x      = 0;
	render_pass_begin_info.renderArea.offset.y      = 0;
	render_pass_begin_info.renderArea.extent.width  = width;
	render_pass_begin_info.renderArea.extent.height = height;
	render_pass_begin_info.clearValueCount          = 2;
	render_pass_begin_info.pClearValues             = clear_values;
    ```

    有三个地方用到了width, height: render area, viewport, scissor. [三者的区别](https://stackoverflow.com/questions/42501912/can-someone-help-me-understand-viewport-scissor-renderarea-framebuffer-size):
    
    * viewport涉及到ndc坐标如何转换到像素坐标.
    * scissor, 用来描述被绘制的区域(不影响坐标转换).
    * render area是framebuffer中会被影响(值会被改变的区域, 需要应用程序确保不会超过render area), 可以供底层进行优化, 比如有些tiles可以不被包含.


    这里录制命令的时候, framebuffer-draw_cmd_buffer

* 其他
    shader中使用了`texture(sampler, uv, lod_bais)`, 用到了lod计算. ui也可以借鉴.

## texture mipmap gen
通过线性滤波的方式, 从上一层拷贝到下一层. `vkCmdBlitImage`.

## subpasses

### gltf文件
参考: https://docs.fileformat.com/3d/gltf/
gltf可以是一个json文件加上其引用的一些文件. subpasses加载的gltf:

```bash
sponza01.gltf # 总的json文件
*.ktx # 纹理image数据
Sponza01.bin # buffer, 主要来存储mesh数据
```

gltf文件中定义了一个node列表, node可以是mesh、camera. 通过index引用node组建一个或多个scene.