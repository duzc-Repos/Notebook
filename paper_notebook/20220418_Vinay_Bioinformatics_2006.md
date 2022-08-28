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
  * 为区分前突触和后突触，实际上应该是$E \in \{0, 1\}^{2M \times K}$

notation for entropy minimization

* $n \in \mathbb{Z}$: the size of the subset of genes
* $S \in \{0, 1\}^{n}$: expression state of certain subset of genes, 其特定状态记为$s_i$
*  $N_0(s_i), N_1(s_i)$: 状态$S$与突触连接无关（0）/有关（0）的次数
  * 实际上是对每个细胞之间的连接观察对应的表达状态，如果对应的细胞之间有连接，则对应的表达状态称为与突触连接有关，否则称为无关

* $C \in {0, 1}$: 突触连接是否存在, 记$c$为

* $GS_l, l=1, \cdots, {2M \choose n}$: 特定基因集



### 2. Entropy minimization and boolen parsimony (EMBP)

#### 2.1 (conditional) Entropy minimization （问题1）

* 原理：给定基因集$GS_i$，最小化突触连接存在的"不确定性" (**conditional entropy**)，使该基因集的表达最能反应突触连接的情况

  > 从随机变量的角度看，$S$是一个$n$维随机向量，每一个维度上的随机变量的取值是二元的；通过
  > $$
  > {\rm P}(S = s_i) = \frac{N_0(s_i) + N_1(s_i)}{K^2}
  > $$
  > 可以得到$S$的概率分布。同理可定义给定表达状态$s_i$的条件下是否存在连接的条件概率分布
  > $$
  > Q(S) = {\rm P}(C=i \mid S = s_i) = \frac{N_i(s_i)}{N_0(s_i) + N_1(s_i)}
  > $$
  > 由此可得给定基因集条件下，突触连接是否存在的条件熵为
  > $$
  > \begin{aligned}
  > {\rm H}(C \mid S) &= \sum\limits_{i=1}^{2^n} {\rm P}(S = s_i) {\rm H}(C \mid S=s_i) \\
  > &= -\sum\limits_{i=1}^{2^n} {\rm P}(S = s_i) \sum\limits_{j=0}^1 {\rm P}(C=j \mid S = s_i)\log {\rm P}(C=j \mid S = s_i)
  > 
  > \end{aligned}
  > $$
  > 条件熵总是非负的，**通过最小化条件熵就可以得到最能解释突触连接存在的一组基因**。

* 为了使取值在0~1之间，对条件熵进行标准化 （$Q_{null} = {\rm P}(C=1)= \parallel A \parallel_F / K^2$）
  $$
  {\rm H}_{norm} = {\rm H}(C \mid S) / {\rm H}(C)
  $$

* **优化问题**
  $$
  \arg\min\limits_{GS_l}\ \  {\rm H}_{norm} (GS_l)
  $$

* 优化方法（search techniques）：

  * 方法1：

    > 1. 给定基因集大小$n$, 随机选择基因集，计算标准化条件熵大小;
    >
    > 2. 随机替换基因，保留准化条件熵小的基因集
    >
    > 3. 为避免局部最小，多次选择初始基因集
    > 4. 对大小为$n+1$的基因集重复上述步骤

  * 方法2：拟退火算法 （以概率形式保留标准化条件熵大的结果继续迭代）



#### 2.2 Boolen parsimony (问题2)

* 原理：
  * 对于给定的基因集，每一个状态$s_i$，都有一个$P(S=s_i)/Q_{null}$与之对应，该值可以表征突触连接前后偏好的基因表达方式。
  * 对于较大的值可以表征为逻辑1，由此得到一个有$n$个变量的逻辑函数。
  * 利用Karnauph map (卡诺图)进行化简，可以得到突触连接表达规则的表示



#### 2.3 Synergy (问题2)

* 原理：$I(S, C) - \max \sum\limits_{S_j} I(S_j; C)$, $S_j$是集合$GS_i$有可能划分(partition)
  * 定义自然：协同就是表示各个部分分开后多出来的
  * 利用上述定义，可以构建**the tree og synergy**，每个节点表示基因集的synergy



### 3. Other computational method

* $W = EAE^T \in \mathbb{R}^{M \times M} $: 每一个元素$W_{mn}$表示匹配特定基因对的连接数目
* $P_{actual} = \frac{W_{mn}}{\parallel A \parallel_F}$：突触连接匹配基因表达对的频率
  * 上述值对overrepresented ordered pairs of genes有偏好，
  * 需要建立空模型消除特定基因表达偏好的影响
* $P_{null} = \begin{cases} \cong \frac{L_m L_n}{K^2} & m \not= n \\ =\frac{L_n(L_n-1)}{K(K-1)} & m=n \end{cases}$： 随即条件下，一对细胞表达基因对$m, n$的频率
  * $L_i = \sum\limits_{j=1}^K E_{ij}$: 表达基因$i$的细胞数量
  * $\frac{L_i}{K}$ : 随机条件下一个细胞表达基因$i$的概率

* $\log_2(\frac{P_{actual}}{P_{null}})$: 可以用于基因对的排序

* 对该方法更详尽的扩展可以参考：
  * A Genetic Model of the Connectome



























