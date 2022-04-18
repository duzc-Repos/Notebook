#  Computational inference of the molecular logic for synaptic connectivity in *C.elegans*



## Question

* 问题1：给定数字$n$，鉴定与前突触和后突出有关的基因集，其联合表达(joint expression)可以预测突触连接的存在
* 问题2：给定上述基因集，找出最简单的逻辑规则(logic rule)来预测突触连接的存在



## Method

### 1. Notation

* $c_i, i=1, \cdots, K$: neurons (280)

* $A \in \{0, 1\}^{K \times K}$: chemical synapses (directed)
* $B \in \{0, 1\}^{K \times K}$: gap junctions (undirected)
* $g_i \in \{0, 1\}^{K}, i=1, \cdots M$:  genes (292)
* $E \in \{0, 1\}^{M \times K}$: gene expression matrix from **Wormbase** (single-cell expression)
  * GFP-tagging or antibody experiments

### 2. Entropy minimization and boolen parsimony (EMBP)

#### 2.1 Entropy minimization

