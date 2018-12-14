## Introduction ##

  对于 graph，以往的研究主要在 model the pairwise relationships of nodes。然而，很多情况下，图上的多个 nodes 共同包含一定的关系，称为 **hyperedge**。
  Hypergraph，特别是 heterogeneous hypergraph 存在的两大问题：
  1. heterogeneous hypergraph 中的 hyperedge 具有不可拆分性，必须将其内部节点共同考虑；
  2. hypergraph 具有稀疏性，需保留更多的 global structure，以获得更好的表达。
  
针对上述问题，提出模型：Deep Hyper-Network Embedding (DHNE) 模型。对问题1，基于非线性网络设计 tuplewise similarity function 以约束 hyperedge 中所有 node 的相似性；对问题2，提出一个 autoencoder framework 以在编码-重构的过程中捕获 neighborhood structure。

## Deep Hyper-Network Embedding ##
### Symbols and Framework ###
文中关键变量被展示，如图：
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/AAAI2018/Structural_deep_embedding_for_hyper-networks/images/fig1.jpg)
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
### Optimization ###
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/AAAI2018/Structural_deep_embedding_for_hyper-networks/images/fig3.jpg)
