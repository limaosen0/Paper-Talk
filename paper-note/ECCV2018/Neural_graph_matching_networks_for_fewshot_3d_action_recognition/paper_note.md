## Introduction ##

  任务：有效地实现3D动作识别（指的是人与环境交互的场景）。目前这种任务存在问题：
  1. labeled RGB 数据很多，但labeled 3D 数据很少；
  2. RGB 方法很难迁移过来。  

针对上述问题，提出 few-shot learning的方法，考虑动作的 inherent structure，利用 Graph representation learning，提出 Neural Graph Matching Networks（NGM），具体：
  1. Graph generation：利用环境种的3D空间信息，生成中间图表达，node 表示 entity，edge 表示 interaction
  2. Graph matching：将 graph matching function 视为一种 metric，进行 few-shot learning。
示意图如下
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/ECCV2018/Neural_graph_matching_networks_for_fewshot_3d_action_recognition/images/fig1.jpg)
尽管前两行在视觉上十分接近，但其实并非同一动作；但后两行的 human-object-environmen t空间结构接近，表达同一个动作。

Contribution:
  1. 对 3D 动作识别引入 few-shot learning，解决少样本训练困难的问题；
  2. 使用图表达来显式地利用 3D 数据的空间信息；
  3. 提出 NGM 模型，生成和匹配图表达，效果良好。

## Neural Graph Matiching Networks ##
模型的整体框架如下，
![image](https://github.com/limaosen0/Paper-Talk/blob/master/paper-note/ECCV2018/Neural_graph_matching_networks_for_fewshot_3d_action_recognition/images/fig2.jpg)
接下来，具体介绍 Graph generation 和 Graph matching 过程。
### Graph Generation ###
1, Node Construction.
节点的获取方式主要由两种：人工标注的含节点标注的数据集，或用目标检测与标注算法标注获得的节点。

2, Edge Learning.
edge 的学习方法是在一个 end-to-end 训练中被训练出来的，可自动推断出节点之间的关系。对两个节点 i 和 j，学得的邻接矩阵可以表示为

<img src="http://latex.codecogs.com/gif.latex? \mathbf{A}_{i,j}=\Phi(x_i, x_j)=MLP_{edge}(|f(x_i)-f(x_j)|)" />
其中 f(·) 表示某个 node 的特征表达，MLP 即为多层感知机。该公式，用两个 representation 之间的绝对值距离来作为是否有 edge 的依据，其中重要的是 f() 是否有效。需要指出的是，我们不希望 f() 只从单个节点中学到，而是希望考虑了这一层中节点的邻居表达，从而学得更全面的特征。考虑邻居的方式就是利用该层学到的邻接矩阵，采用 graph convolution 技术，具体如下

<img src="http://latex.codecogs.com/gif.latex? f^{(k+1)}(x_i)=\sigma((D^{(k)})^{-\frac{1}{2}}\mathbf{A}^{(k)}_{i}(D^{(k)})^{-\frac{1}{2}}f^{(k)}(x_i)W_{edge})" />
<img src="http://latex.codecogs.com/gif.latex? \mathbf{A}^{(k)}_{i,j}=MLP_{edge}(|f^{(k)}(x_i)-f^{(k)}(x_j)|)" />

### Graph Matching ###
这是一种 inexact graph matching 问题，因为节点数不同，节点之间的关系千变万化。

### Learning Optimization ###
最小化负的 log-probability，即 maximize log-likelihood。


