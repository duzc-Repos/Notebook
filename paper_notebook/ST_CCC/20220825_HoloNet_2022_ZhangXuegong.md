# Decoding functional cell–cell communication events by multi-view graph  learning on spatial transcriptomics

* biorxiv: [Decoding functional cell–cell communication events by multi-view graph learning on spatial transcriptomics (biorxiv.org)](https://www.biorxiv.org/content/10.1101/2022.06.22.496105v2.full.pdf)



## Method

### 0. Datasets and data pre-processing

* Human Breast Cancer, Block A Section 1 (10x Genomics Visium): 24923 genes in 3798 spots
* Human Breast Cancer(Wu et al, NG, 2021), select 1160920F of 6 samples: 28402 genes in 4895 spots
* connectomeDB2020: 配体受体数据库
* preprocessing:
  * 过滤UMI< 500的spots, 过滤spot表达数量小于3的gene
  * LogNormalize in Seurat
  * 利用GSE118390做细胞类型注释
    * malignant: basal-like1, basal-like2, luminal-AR, mesenchymal
    * stromal and immune: macrophage, B cell, T cell, stromal, endothelial cell
  * 过滤L-R pair: 配体或受体表达的细胞数量小于30%则滤除



### 1. Multi-view CE network

* $G = \{G^{(v)} = (U, E^{(v)}) \mid v \in V \}$: multi-view network
  * $V$: the set of view, representing **L-R pairs**
  * $U$: the set of node, representing single cells or **spots**
  * $G^{(v)}$: a certain view, directed weighted graph
    * $E^{(v)} = \{ w_{ij}^{(v)} \mid i, j \in U\}$: 代表由特定L-R pair $v$介导的细胞$i, j$之间的communication events



#### 1.1 Weighting Strategy

> **Fick's second law**: considering a one diemensional diffusion:
> $$
> c(x, t) = \frac{N}{\sqrt{4\pi Dt}} \exp(-\frac{x^2}{4Dt})
> $$
>
> * c(x, t): 某一配体在$t$时刻，扩散至$x$位置时的浓度
> * $N$: 起始扩散浓度
> * $D$: 扩散系数

* $C_{i,j}^{L_v}$: 配体$L_v$由细胞$i$的位置，扩散至细胞$j$位置时的浓度

* $e_{i}^{L_v}$: 配体$L_v$在细胞$i$中的表达水平

* $w_{Lv}$: 反应配体$L_v$的扩散能力
  $$
  c_{ij}^{L_v} \propto e_i^{L_v} \exp(-\frac{d_{ij}}{w_{L_v}})
  $$
  假设上述的比例系数为$1$, 根据**质量作用定律**可得：
  $$
  w_{ij}^{(v)} = c_{ij}^{L_v} e_j^{R_v} = e_i^{L_v} e_j^{R_v} \exp(-\frac{d_{ij}}{w_{L_v}})
  $$

#### 1.2 edge specificities

* 选取$n$对在平均表达水平上与配体和受体相近的基因作为背景基因
* 计算$n$对背景基因的边的权重，组合形成背景分布
* 计算P值，过滤P>0.05的边



#### 1.3 Visualization

* L-R pair CE hotspot at spot level: **degree centrality** $dc_{i, v}$ and **eigenvector centrality** $ec_{i,v}$
* CE network at cell-type level: $w_{A, B}^{v} = \sum_\limits{i \in A, j \in B} w_{i, j}^{v}$
* L-R pair clustering:  
  * $d_{u, v} = \sqrt{\sum_\limits{i=1}^n (ec_{i, u} - ec_{i, v})^2}$
  * 利用距离矩阵做层次聚类



### 2. multi-view graph learning

目的：通过multi-view CE network预测靶基因的表达，解释CE如何影响这些基因

* $X \in \mathbb{R}^{N \times C}$: 输入，细胞类型矩阵，one-hot encode
* $A = \{A^{(v)} \in \mathbb{R}^{N \times N} \mid v \in V\}$: 输入，adjacency matrices of multi-view CE network $G$

* $\hat{E}$: 图神经网络的输出，代表特定靶基因 $t$ 的表达
  $$
  \hat{E}_t  = {\rm HoloGNN_t}(X, A)
  $$



#### 2.1 select target genes:

* highly variable genes (highly_variable_genes from scanpy, min_mean=0.05, min_disp=0.5)
* \> 50% cells expressed
* not mitochondrion, ligand or receptor genes



#### 2.2 adjacency matrices preprocessing:

* $c_i^{A}$: 细胞$i$是否属于类型$A$，或者spots中A类型的比例
* $s_{ij} = \sum_\limits{k } c_i^k c_j^k$: 细胞类型相似性
* ${A'}_{ij}^{(v)} = (1 - s_{ij})A_{ij}^{(v)}$
* $\hat{A}^{(v)} = I  + D^{(v)-\frac{1}{2}} {A'}^{(v)} D^{(v)-\frac{1}{2}}$ : 标准化



#### 2.3 model architecture

![image-20220825153420494](C:\Users\86188\AppData\Roaming\Typora\typora-user-images\image-20220825153420494.png)

* decomposite the expression $E$ into two components:
  * $E_0$: baseline expression determined by its cell type
  * $\Delta E$: expression change caused by CEs

* For $E_0$:

  * $\hat{E_0} = {\rm linear(X)}$

* For $\Delta E$:

  * $\hat{A}^{(v)}X \in \mathbb{R}^{N \times C}$: 代表通过L-R pair $v$, 细胞$i$和细胞类型$j$之间的general CE strengths

  * $W^{(v)} \in \mathbb{R}^{C \times H}$: model parameters, $H$ is the hidden layer dimension, 通常设置$H=C$

  * $B^{(v)} \in \mathbb{R}^{N \times H}$: bais matrix

  * $c_v$: attention mechanism, learnable parameters

  * $z_\Delta$: final embedding
    $$
    z_\Delta = {\rm ReLU}(\sum_\limits{v \in V} c_v (\hat{A}^{(v)}XW^{(v)} + B^{(v)} ))
    $$

  * $\hat{\Delta E} = {\rm linear}(z_\Delta)$

* $\hat{E} = {\rm sigmoid}( \hat{E_0} + \hat{\Delta E} )$
* ${\rm loss = MSE}(E, \hat{E}) = \frac{1}{n} \sum_\limits{i}(e_i - \hat e_i)^2$

#### 2.4 model training

* 85% cells as training, 15% as validation
* 500 epochs, 选择在validation数据上MSE最小的作为最终的model
* Adam optimizaer (lr=0.1, weight decay=0.0005)
* step learning rate decay strategy (step size=10, gamma=0.9)

* robustness and reliability:
  * 重复训练模型（FCE=50 times, target genes=5 times）
  * 最后的表达谱用多次训练的平均值



#### 2.5 model interpretation

* $\hat{\Delta E}/\hat{E_0}$: 计算每个细胞类型的值，该值高的表示该细胞类型受该FCE的影响比较大
* $c_v$: L-R pairs mediating FCEs
* 鉴定在FCE中主要的sender和receiver的细胞类型：
  * $w_{C, i}^{(v)} = (\hat{A}^{(v)}X)_{i, C}\sum_\limits{k}W_{C, k}^{(v)}$: general FCE strengths from sender cell type $C$ to cell $i$
  * cell-type-level FCE network: $w_{C, C'}^{(v)} = \sum_\limits{u \in C'} w_{C, i}^{(v)}$
  * 



### 3. Baseline model

#### 3.1 cell-type only model

$$
\hat{E}_{cell-type-only} = {\rm sigmoid}({\rm linear}(X))
$$



#### 3.2 spatial proximity relationship model

* $A_{i, j}^{prox} = \exp(- \frac{d_{ij}}{ {\rm mean}(w_{V_v}) })$

$$
\hat{E}_{prox} = {\rm sigmoid}({\rm linear}({\rm ReLu}(A^{prox}XW+B))) + {\rm sigmoid}({\rm linear}(X))
$$



#### 3.3 Lasso model using LR  and cell-type information

* $X_{v, j}^{lasso} = \sum_\limits{j} A_{i, j}^{(v)}$

$$
\hat{E}_{lasso} = {\rm Lasso}(\begin{bmatrix} X^{lasso} \mid X \end{bmatrix})
$$

