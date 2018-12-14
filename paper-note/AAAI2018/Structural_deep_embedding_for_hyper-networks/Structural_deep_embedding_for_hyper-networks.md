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
分层介绍模型功能（first, second and third layer）。

首先，关于保留 global structure (second-order proximity)，提出一种autoencoder解决方法，如first layer。具体地，先得到 hypergraph 的 adjacent matrix **A**。对 hypergraph **G=（V, E）**，先得到其 incidence matrix **H**，再得到 node degree matrix **D**v，则

<img src="http://chart.googleapis.com/chart?cht=tx&chl= \mathbf{A}=\mathbf{HH}^{\rm{T}}-\mathbf{D}_v" style="border:none;">

在 adjacent matrix 中，第i行表示第i个 node 的 neighborhood structure。将 adjacent matrix 输入至一个 autoencoder 来保存结构信息，其中 encoder 和 decoder 都是非线性映射，可以被表示为

<img src="http://chart.googleapis.com/chart?cht=tx&chl= \mathbf{X}_i=\sigma(\mathbf{W}^{(1)}\mathbf{A}_i \+ \mathbf{b}^{(1)})" style="border:none;">
<img src="http://chart.googleapis.com/chart?cht=tx&chl= \hat{\mathbf{A}}_i=\sigma(\hat{\mathbf{W}}^{(1)}\mathbf{A}_i \+ \hat{\mathbf{b}}^{(1)})" style="border:none;">

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

接着，关于tuplewise similarity function的设计，不能采用node之间的线性组合运算（文中证明）。

### Loss Function ###
### Optimization ###
