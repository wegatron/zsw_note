# Face Landmark detection
以此项目, 作为深度学习入门的训练.

## 调研相关算法
3DDFA v2(space-x), FAN(DECA)

3DDFA(旧版本)与FAN(face-alignment)对比: https://github.com/1adrianb/face-alignment/issues/122
>Hi @cristinasegalin, while the authors of 3DDFA do not offer a direct comparison, judging from the results reported in [https://arxiv.org/pdf/1804.01005.pdf, Table 3] and the ones from [https://ibug.doc.ic.ac.uk/media/uploads/documents/trigeorgis2016mnemonic.pdf (MDM), Table 1] we can notice that new 3DDFA has approximatevely the same accuracy as CFSS on 300W. Since MDM significantly outperforms CFSS and our method surpasses MDM, most likely on average our method will outperform the improved 3DDFA results by a helthy margin. In addition, our method doesn't suffer from the failure cases reported in Fig. 20. That beign said, while this is the most likely conclusion, the best will be to evaluate them on a common dataset, ie. 300W or LS3D-W-Balanced.

3DDFA v2 没有提供相关训练代码, 选择3DDFA进行学习.

## 3DDFA
