# Knowledge-graph-based cell-cell communication inference for spatially resolved transcriptomic data with SpaTalk



## Method

### Algorithm Summary:

* cell-type decomposition: 推断细胞类型的组成
* spatial LRI enrichment: 推断空间临近的介导细胞通讯的L-R相互作用



### Cell type decomposition

* $T \in \mathbb{R}^{n \times s}$: ST data matrix (n genes, s spots/cell)
* $S \in \mathbb{R}^{n \times c}$: single cell data (n genes, c cells), 作为参考数据集确定细胞类型
* $X \in \mathbb{R}^{n \times k}$: 细胞类型平均表达谱 (n genes, k cell-types)

* $\beta \in \R_+^{k \times s}$: coefficient matrix, 代表每个spots的组成成分

$$
\arg \min_\limits{\beta \geq 0} {\rm loss}(Y - X\beta) + \lambda_1R_1(\beta) + \lambda_\alpha R_\alpha(\beta) + \lambda_2 R_2(\beta)
$$

* **求解**即可得到系数矩阵

  * 当可以达到单细胞分辨率时，系数中最大的所对应的细胞类型为该细胞的细胞类型

  * 对spots $j$ in ST data：假定最多有$M$个细胞，通常设置为30 (10x), 1 (Slide-seq)
    $$
    \omega_i = {\rm round}(M\beta_{ij}) \ \ i=1, \cdots M
    $$

* 随机从$S \in \mathbb{R}^{n \times c}$中取$m = \sum\limits_{i=1}^k \omega_i$个细胞，利用如下方法与ground truth作比较
  $$
  \arg \min\limits_{m \leq M} \sum_\limits{i=1}^n (Y_i - \sum_\limits{j=1}^m \epsilon_i^{j})
  $$

* 为每个抽取的细胞分配坐标$(\hat x, \hat y)$：
  * $(x_0, y_0)$: 某个spot的坐标
  * $d_{min}$: 距离该spot最近的spots的距离
  * $\theta \in (0, 360]$: 新生成的点到$(x_0, y_0)$的角度
    * $Q$: $Q$个临近的spots
    * $R_i$: 第$i$个近邻spots中
  * $\alpha$: $d_{min}$的权重



### Spatial LRI enrichment

* $D$: 从上一步重建出的单细胞空间转录组的细胞之间的欧氏距离

* 根据$D$ 构建选择$K$近邻构建cell graph network (K=10)

* $C_{A_i, B_j}$: 细胞类型$A$的$i$配体与细胞类型$B$的$j$配体之间有共表达的细胞对数

* $C = \{C_{A_i, B_j}^{1}, \cdots, C_{A_i, B_j}^{Z} \}$: $Z$次permutation
  $$
  P_{A_i, B_j} = {\rm card}\{x \in C\ \mid x \geq C_{A_i, B_j} \}/Z
  $$

  * 过滤$P_{A_i, B_j} < 0.05$
  * intercellular score of LRI $S_{A_i, B_j}^{inter} = 1 - P_{A_i, B_j}$

* enrich LRI that activate downstream genes (TFs, target genes, related pathways)

  * knowledge graph: 整合LRI (CellTalkDB), pathways(KEGG, Reactome), TFs(AnimalTFDB)构建LRT-KG, 共表达系数作为网络权重，recepter为query node

  * 在LRT-KG上进行随机游走，选择在Z次迭代中10步之内的$t$个TF，利用该方法计算相应概率

  * 整合共表达的TF以及相应的target genes，可计算intracelluar score

    * $\theta$: target gene的数量
    * $\eta$: receptor到TF的步数

    $$
    S^{intra}_{A_i, B_j} = \sum_{k=1}^t \theta_k \times p_k / \eta_k
    $$

* final score
  $$
  S_{A_i, B_j} = \sqrt{S_{A_i, B_j}^{inter} \times {\rm sigmoid}(S_{A_i, B_j}^{intra}) }
  $$
  

### 方法比较

* dataset：STARmap， MERFISH，seqFISH+。
  * 利用上述方法进行单细胞分割，在聚合成spots，形成**benchmark dataset**
* spatial proximity significance of inferred LRIs
  * number of expressed LRIs between sender and receiver pairs and all cell-cell pairs