# Regularization Optimization

## Algorithm Design

对于任意一对相连的三角形, 添加平面正则项约束:
$$
\begin{aligned}
\mathbf{0} &= w_1 \mathbf{v}_1 + w_2 \mathbf{v}_2 + w_3 \mathbf{v}_3 + w_4 \mathbf{v}_4\\
0 &= w_1 + w_2 + w_3 + w_4\\
1 &= w_1^2 + w_2^2 + w_3^2 + w_4^2
\end{aligned}
$$
优化目标函数定义为:
$$
\begin{aligned}
&\min_{\mathbf{x}} \parallel \mathbf{Mx} \parallel^2 + w_r^2\parallel \mathbf{Ax} \parallel^2 \\
\Rightarrow &\min_{\mathbf{c}} \parallel \mathbf{MPc} \parallel^2 + w_r^2\parallel \mathbf{APc} \parallel^2, \; &s.t. \quad \parallel \mathbf{c} \parallel = 1.\\
\Rightarrow &\min_{\mathrm{c}} \parallel \begin{bmatrix} \mathbf{MP}\\w_r\mathbf{AP} \end{bmatrix} \mathbf{c} \parallel^2, \; &s.t. \quad \parallel \mathbf{c} \parallel = 1.
\end{aligned}
$$
这里, $\parallel \mathbf{Mx} \parallel^2$ 表示了特征点对应位置的约束, $\parallel \mathbf{Ax} \parallel^2$是正则项约束(由$w$构成). 在我们的应用场景中, $\mathbf{c}$ 是控制点(跟踪到的特征点), $\mathbf{x}$ 是网格上所有的点, 它们之间可以互相转换.
$$
\left\{
\begin{aligned}
&\mathbf{c} = \mathbf{P}_c \cdot \mathbf{x}\\
&\mathbf{x} = \mathbf{P}\cdot \mathbf{c}
\end{aligned}
\right.
$$

这里, $\mathbf{P}_c$ 是一个$3N_c \times 3N_v$ 的矩阵, 在控制点(特征点)的位置为1, 其他点为0. $\mathbf{P}$ 可以由$\mathbf{A}$计算得到, 令
$$
\begin{aligned}
\mathbf{x} = \begin{bmatrix}
\mathbf{c}\\
\mathbf{\lambda}
\end{bmatrix}\\
\mathbf{A} = [\mathbf{A}_c | \mathbf{A}_\lambda]
\end{aligned}
$$
可以推出:
$$
\mathbf{P} = \begin{bmatrix}
\mathbf{I}\\
-(\mathbf{A}_\lambda^T\mathbf{A}_\lambda)^{-1}\mathbf{A}_\lambda^T\mathbf{A}_c
\end{bmatrix}
$$

## Algorithm Pipeline

我的理解:

1. 数据读取/加载: 3D triangle mesh data, camera intrinsic params, model frame.
2. 根据拓扑结构计算矩阵$\mathbf{A}$, 再由矩阵$\mathbf{A}$计算矩阵$\mathbf{P}$. 
3. 视频帧与参考帧之间的特征点匹配, 构建重投影误差矩阵$\mathbf{M}$.
4. 求解$\mathbf{c}$, 从而计算得到$\mathbf{x}$.
5. $\mathbf{x}$ 3d坐标的矫正(修改深度以及带约束的优化).
6. 重复步骤3,4,5.

作者Matlab代码:

1. 数据读取/加载: 3D triangle mesh data, camera intrinsic params, model frame.

2. 预计算$\mathrm{RegMat},\mathrm{CtrlMat},\mathrm{RegCtrCtrReg}, \mathrm{AP}, \mathrm{APtAP}$
   $$
   \begin{aligned}
   \mathrm{RegMat} &= \begin{bmatrix} ... & w_{i0} & ... & w_{i1} & ... & w_{i2} & ... & w_{i3} & ...
   \end{bmatrix}\\\\
   \mathbf{A} &= \mathbf{I} \otimes \mathrm{RegMat}\\
   \mathbf{P} &= \mathbf{I} \otimes \mathrm{CtrlMat}\\
   \mathrm{RegCtr} &= \mathrm{RegMat} \cdot \mathrm{CtrlMat}\\
   \mathrm{RegCtrCtrReg} &= \mathrm{RegCtr}^T \cdot \mathrm{RegCtr}\\
   \mathrm{AP} &= \mathbf{I} \otimes \mathrm{RegCtr}\\
   \mathrm{APtAP} &= \mathbf{I} \otimes \mathrm{RegCtrCtrReg}
   \end{aligned}
   $$
   这里$\mathrm{RegMat}$ 行数为三角形pair的数量, 列数为网格定点数量.

3. 视频帧与参考帧之间的特征点匹配, 通过正则项误差来剔除outlier, `register_deformable_2D_2`.

   在作者的Matlab代码中, outlier剔除部分, 优化的是$\min_\mathbf{c} \parallel \mathbf{BPc} - \mathbf{U}\parallel^2$, $\mathbf{U}$是匹配得到的特征点uv.

4. 正则项和投影误差合并优化.

   

5. 重复3,4.

## Reference

> Template-based Monocular 3D Shape Recovery using Laplacian Meshes

