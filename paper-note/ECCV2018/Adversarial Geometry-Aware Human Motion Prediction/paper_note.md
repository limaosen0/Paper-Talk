## Introduction ##

  任务：给定一个输入3D骨架序列时，预报短期或长期的人体姿态。基于框架：recurrent encoder-decoder。目前这种任务存在问题：
  1. prediction discontinuities
  2. fail to predict in longer time  

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

**Geodesic loss:** 目的是拟合生成样本， measure the distance between predicted/groundtruth 3D rotations。事实上，Euclidean loss 会忽视几何结构信息，而文中采用 geodesic loss。对于基为<img src="http://latex.codecogs.com/gif.latex? \mathbf{u}=(u_1, u_2, u_3)^{\rm{T}}" />的 rotation：<img src="http://latex.codecogs.com/gif.latex? \mathbf{\theta}=(\alpha, \beta, \gamma)" />，对应旋转矩阵为<img src="http://latex.codecogs.com/gif.latex? \mathbf{R}=[\mathbf{\theta} \cdot \mathbf{u}]_{\times}" />，两个符号分别是 inner/outer product。


文中关键变量被展示，如图：

基于上述变量，定义了 hyper-network, the first-order proximity and the second-order proximity.

提出的DHNE模型的框架如图所示
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/AAAI2018/Structural_deep_embedding_for_hyper-networks/images/fig2.jpg)
分层介绍模型功能（first, second and third layer）。首先，关于保留 global structure (second-order proximity)，提出一种autoencoder解决方法，如first layer。具体地，先得到 hypergraph 的 adjacent matrix **A**。对 hypergraph **G=（V, E）**，先得到其 incidence matrix **H**，再得到 node degree matrix **D**v，则

<center><img src="http://latex.codecogs.com/gif.latex? \mathbf{A}=\mathbf{HH}^{\rm{T}}-\mathbf{D}_v" /></center>

在 adjacent matrix 中，第i行表示第i个 node 的 neighborhood structure。将 adjacent matrix 输入至一个 autoencoder 来保存结构信息，其中 encoder 和 decoder 都是非线性映射，可以被表示为

<img src="http://latex.codecogs.com/gif.latex? \mathbf{X}_i=\sigma(\mathbf{W}^{(1)}\mathbf{A}_i + \mathbf{b}^{(1)})" />
<img src="http://latex.codecogs.com/gif.latex? \hat{\mathbf{A}}_i=\sigma(\hat{\mathbf{W}}^{(1)}\mathbf{A}_i + \hat{\mathbf{b}}^{(1)})" />

--------
另一方面，关于tuplewise similarity function的设计，不能采用node之间的线性组合运算（文中证明），故“尝试”采用非线性运算的方式，如 second layer and third layer。 The second layer 是一个全连接非线性神经网络，对三个节点，将其在 the first layer 输出的中间表达输入其中，得到一个 latent representation，即

<img src="http://latex.codecogs.com/gif.latex? \mathbf{L}_{ijk}=\sigma(\mathbf{W}_{a}^{(2)}\mathbf{X}_i^a + \mathbf{W}_{b}^{(2)}\mathbf{X}_j^b + \mathbf{W}_{c}^{(2)}\mathbf{X}_k^c + \mathbf{b}^{(2)})" />
随后再将 latent representation 映射至一个 probability space，即

<img src="http://latex.codecogs.com/gif.latex? \mathbf{S}_{ijk} = \mathcal{S}(\mathbf{X}_i^a, \mathbf{X}_j^b, \mathbf{X}_k^c) = \sigma(\mathbf{W}^{(3)}\mathbf{L}_{ijk}+\mathbf{b}^{(3)})" />
对同一个 hyperedge 中的 node 需要此量尽量大，当三个点不属于同一个 hyperedge 时，需要此量尽量小。

### Loss Function ###
对于 autoencoder 模块而言，其 loss 为

<img src="http://latex.codecogs.com/gif.latex? \mathcal{L}_1 = \sum_t\|sign(\mathbf{A}_i^t)\odot(\mathbf{A}_i^t-\hat{\mathbf{A}}_i^t)\|_F^2" />
对于 tuplewise similarity function 部分，其loss 为

<img src="http://latex.codecogs.com/gif.latex? \mathcal{L}_2 = -(\mathbf{R}_{ijk}\log\mathbf{S}_{ijk}+(1-\mathbf{R}_{ijk})\log(1-\mathbf{S}_{ijk}))" />
其中 **R** 表示 hyperedge 的真实存在情况。最后，总体的 loss 可以表示为

<img src="http://latex.codecogs.com/gif.latex? \mathcal{L} = \mathcal{L}_1+\alpha\mathcal{L}_2" />

### Optimization ###
模型的训练算法如图所示。

![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/AAAI2018/Structural_deep_embedding_for_hyper-networks/images/fig3.jpg)
