## Introduction ##

  任务：有效地实现3D动作识别（指的是人与环境交互的场景）。目前这种任务存在问题：
  1. labeled RGB数据很多，但labeled 3D数据很少；
  2. RGB方法很难迁移过来。  

针对上述问题，提出 adversarial geometry-aware encoder-decoder (AGED) 模型，具体：
  1. 几何结构约束 & temporal smoothness and continuity regularization
  2. Framewise geodesic loss 而不是Euclidean loss
  3. Adversarial training mechanism, 2 discriminators (fidelity & continuity)
  
效果如图
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/ECCV2018/Adversarial%20Geometry-Aware%20Human%20Motion%20Prediction/images/fig1.jpg)
其中，第二行的输入帧与预测帧之间明显有断裂，第三行的长期动作具有较大误差，均不如 AGED 模型。

## Adversarial Geometry-aware Encoder-decoder Model ##
AGED 包含了一个encoder-decoder predictor 和两个 discriminators, 其框架如图所示。
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/ECCV2018/Adversarial%20Geometry-Aware%20Human%20Motion%20Prediction/images/fig2.jpg)
### Geometry-aware encoder-decoder predictor ###
在 predictor 中，encoder 从输入序列中学习 hidden representation，解码器根据 representation 和 seed motion frame 来生成未来动作。此外，attention 机制和 biLSTM 也被结合至网络中。具体地，predictor (encoder & decoder) 中的循环单元采用 GRU。使用 residual connection 来建模运动速度，而不是直接在绝对的角度上进行操作。每个输入序列与 action class 相连被共同送入模型。

**Geodesic loss:** 目的是拟合生成样本， measure the distance between predicted/groundtruth 3D rotations。事实上，Euclidean loss 会忽视几何结构信息，而文中采用 geodesic loss。对于旋转轴为<img src="http://latex.codecogs.com/gif.latex? \mathbf{u}=(u_1, u_2, u_3)^{\rm{T}}" />的 rotation：<img src="http://latex.codecogs.com/gif.latex? \mathbf{\theta}=(\alpha, \beta, \gamma)" />，对应旋转矩阵为<img src="http://latex.codecogs.com/gif.latex? \mathbf{R}=[\mathbf{\theta} \cdot \mathbf{u}]_{\times}" />，两个符号分别是 inner/outer product。说实话完全没看懂这在扯什么，由 Euler角定义旋转矩阵的方法为：

![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/ECCV2018/Adversarial%20Geometry-Aware%20Human%20Motion%20Prediction/images/fig3.jpg)

给定两个旋转矩阵<img src="http://latex.codecogs.com/gif.latex? \mathbf{R}" />和<img src="http://latex.codecogs.com/gif.latex? \hat{\mathbf{R}}" />，可得到在两个矩阵之间不同角度的旋转矩阵，计算方法

<img src="http://latex.codecogs.com/gif.latex? \log\hat{\mathbf{R}}\mathbf{R}^{\rm{T}}=A\frac{arcsin(\|A\|_2)}{\|A\|_2}" />
所以，geodesic distance 被定义为

<img src="http://latex.codecogs.com/gif.latex? \mathbf{d}_G(\hat{\mathbf{R}},\mathbf{R})=\|\log\hat{\mathbf{R}}\mathbf{R}^{\rm{T}}\|_2" />
相应的，geodesic loss 被定义为

<img src="http://latex.codecogs.com/gif.latex? \mathcal{L}_{geo}=\sum_{j=n+1}^{n+m}\sum_{k=1}^{K/3}\mathbf{d}_G(\hat{\mathbf{R}}_j^k,\mathbf{R}_j^k)" />

### Fidelity and continuity discriminators ###
模型中包含了两个discriminator，分别是：
  1. 非条件的保真度 (unconditional, fidelity) discriminator：区别短期序列，衡量smoothness。
  2. 条件的连续度 (conditional, continuity) discriminator：区别长期序列，衡量coherence。
  
Loss 不做赘述，参考论文。


