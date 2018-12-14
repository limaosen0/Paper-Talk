## Introduction ##
  自然界中存在众多图结构数据，对于图，以往的研究方案主要在model节点的pairwise关系。然而，很多情况下，图上的多个nodes共同包含一定的关系，称为**hyperedge**。本文针对heterogeneous graph存在的两大问题进行解决：
  + heterogeneous hypergraph中的hyperedge具有不可拆分性，必须将其内部节点共同考虑；
  + hypergraph具有稀疏性，需保留更多的global structure，以获得更好的表达。
