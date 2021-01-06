## 2021.1
- [ ] 文字demo 1~2天
    接入decode得到的深度纹理, 设置文字深度, 实现遮挡效果

- [ ] 算法移植 2~3天
    - [ ] 将solver的方式转化为filter的方式, 在GPU上实现.
        使用filter的方式需要较大的滤波半径, 且效果与solver相比差很多.
        <figure class="image">
        <img src="rc/depth_img_edge_filter.png">
        <em><center>solver(左)和filter(右)结果对比</center></em>
        </figure>

    - [ ] 减少待优化量
        事实上我们仅需要对深度图的edge区域进行深度优化(抗锯齿)
        <figure class="image">
        <img src="rc/depth_edge_area.png" width=300>
        <img src="rc/depth_edge_partial_solve.png">
        <em><center>深度图边界(上). solver完整结果(左下)和只优化深度图边界(右下)的对比</center></em>
        </figure>

- [ ] 性能优化

- [ ] 效果调优

- [ ] 抖动优化
    使用光流对应像素点, 然后做加权平均(smooth)