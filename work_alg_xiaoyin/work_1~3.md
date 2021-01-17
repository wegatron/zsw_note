## 2021.1
- [x] 文字效果接入
    接入decode得到的深度纹理, 设置文字深度, 实现遮挡效果.
    UpdateFrameBuffer()函数中获取当前深度, 并设置给textRender.
    textRender中对于深度值大于某个值的fragment discard掉即可.

- [ ] libqvar
    支持双摄, 以及错误码返回

- [ ] fiting支持

- [ ] rendering engine更新


- [ ] 算法移植 2~3天
    - [x] 将solver的方式转化为filter的方式, 在GPU上实现.
        使用filter的方式需要较大的滤波半径, 且效果与solver相比差很多.
        <figure class="image">
        <img src="rc/depth_img_edge_filter.png">
        <em><center>solver(左)和filter(右)结果对比</center></em>
        </figure>

    - [ ] edge detector移植
        使用内置的MPSImageCanny进行滤波
    
    - [ ] 减少待优化量
        事实上我们仅需要对深度图的edge区域进行深度优化(抗锯齿)
        <figure class="image">
        <img src="rc/depth_edge_area.png" width=300>
        <img src="rc/depth_edge_partial_solve.png">
        <em><center>深度图边界(上). solver完整结果(左下)和只优化深度图边界(右下)的对比</center></em>
        </figure>

        - [ ] 在使用depth图的梯度阈值选取边界部分的像素点作为优化对象
        - [ ] 使用共轭梯度法求解得到优化后的深度图

- [ ] 效果调优
    - [ ] 边界检测优化
    - [ ] 抖动优化
        使用光流对应像素点, 然后做加权平均(smooth)