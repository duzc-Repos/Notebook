# NicheNet: modeling intercellular communication by linking ligands to target genes



## Method



### Data Collection

* L-R interaction: 
  * KEGG, Ramilowski et al, Harmonizome.
  * 通过PPI数据库中相互作为的gene为L-R进行预测
* Signaling and PPI:
  * Omnipath, PathwayCommons, InWeb_InBioMap, ConsensusPathDB, Vinayagam et al, EVEX, *KEA, PhosphoSite, DEPOD, Harmonizome kinase-substrate predictions (Harmonizome)*
  * 方向处理：
    * 双向的相互作用都有记录，则保留双向的
    * 如果只有单向的记录，则只保留单向的
    * 去除自连接，以及连向配体的

* Gene regulatory interaction:
  * RegNetwork, TRRUST, HTRIDB, ReMap, EVEX, PathwayCommons, Ontogenet, *CHEA, ENCODE, JASPAR, TRANSFAC, MOTIFMAP, GEO, MSigDB (Harmonizome)*

* 经过处理后，每个数据资源都被处理成有向图的形式，记录为$G = <V, E, A>$



### Prior model of ligand-target regulatory potential

#### Integrated weighted ligand-signaling network

* weighted sum of adjacency metrices $W = \sum_\limits{i=1}^n w_iA_i$
  * $w_i$代表了第$i$个网络对总体的贡献
* hub correction: $W_{corrected} = WD^{-h}\ \ \ (h \in [0, 1])$
  * $D$: 入度对角矩阵
  * 矫正是为了减轻hub的支配效应可能对模型产生的负面影响
  * 方法是按入度成比例降低连接到hub的边的权重
* ligand-target regulatory potential score: $v = (1-d) \times u + d \times W \times v$
  * 使用Personalized PageRank(PPR)方法，对一个配体对所有基因的重要性进行打分
    * $d$: damping factor
    * $v$: 随机游走的稳态分布
    * $u$: 偏好向量，其所有元素的和为1
* $PPR$ vector cutoff:
  * 通过保留度数随机化，为每一个score建立一个背景分布，保留score值在特定分位数之上的值，区间为$[0.9, 0.999]$，该值通过自动优化方法确定
* 通过对每一个配体实施$PPR$以及cutoff，最终获得$n \times m$的ligand-regulator matrix

#### Integrated weighted gene regulatory network

* 参考上述方法就见gene regulatory network

####  ligand-target regulatory potential

$$
l_{ij} = \sum_\limits{i=1}^m PPR_{ik} * GRN_{kj}
$$



### Leave-one-in model and One-versus-one-versus-one model

* Leave-one-in:
  * 一个来源的L-S + 所有来源的GRN （超参数与整体相同）
  * 所有来源的L-S + 一个来源的GRN（超参数与整体相同）
  * 可以评估不同数据资源的互补性以及每个数据资源的特有信息

* one-versus-one-versus-one:
  * 一个来源的L-S + 一个来源的GRN
  * 可以评估为何每层都需要不同来源的数据进行整合，而不是直接用一个比较全面的数据