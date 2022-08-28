# node2vec: Scalable Feature Learning for Networks





## 2. Related work

* 目的：对网络数据实现全自动的特征提取
* 无监督的特征学习方法：通常是探究spectral properties
  * 常用方法：PCA，IsoMap， etc
  * 缺点：
    * 计算耗时：矩阵的特征分解对大矩阵运算不友好
    * 优化的目标函数不够robust，得到的特征不能适用于多种任务
* 近期方法：representational learning for natural language processing
  * Skip-gram model / Word2Vec
  * network representation method: Deepwalk, LINE
    * 本质上都是网络上的抽样方法：**从网络上抽取一个节点序列**，应用于神经网络模型
    * 这些方法都不够灵活，无法适用不同的任务



## 3. Framework

* Notation
  * $G(V, E)$: 一个给定网络
  * $f:V \rightarrow \mathbb{R}^d$: 节点特征表示的映射函数，
    * $d$是结点表示的维数
    * $f \in \mathbb{R}^{|V| \times d}$: 是一个矩阵，有$|V| \times d$个参数
  * $N_S(u)$: 通过**抽样策略$S$**产生的结点$u$的近邻
* objective function $\max\limits_{f} \sum_\limits{u \in V} \log {\rm Pr}(N_S(u) \mid f(u))$
  * 给定结点$u$的表示的条件下，观测到其近邻的概率最大
* 为了使上述优化问题更容易处理，需要做一些假设：
  * 条件独立假设：${\rm Pr}(N_S(u) \mid f(u)) = \prod_\limits{n_i \in N_S(u)} {\rm Pr}(n_i \mid f(u))$
  * 特征空间对称性：源结点和近邻结点之间的效应是对称的，即${\rm Pr}(n_i \mid f(u)) = \frac{\exp (f(n_i)^Tf(u))}{\sum_\limits{v \in V} \exp (f(v)^Tf(u))}$

* 由上述假设，木匾函数可以简化为$\max_\limits{f} \sum_\limits{u \in V} \left[ -\log Z_u + \sum_\limits{n_i \in N_S(u)}f(n_i)^Tf(u) \right]$
  * 由于partition function计算耗时，采用negative sampling进行近似



### 3.1 Classic search strategies

* 网络中的结点存在两种相似性：
  * homophily: 处于同一community的结点
  * structural equivalence: 比如不同网络的hub结点
* BFS：更能反应structural equivalence。
  * 通过对结点近邻的刻画，就可以推断出该节点是不是hub。这种搜索反应的实际上是一种local的性质
  * 并且在搜索过程中，近邻的结点会重复出现，降低了刻画分布的方差
* DFS：更能反应homophily
  * 实际上反应的是一种相对global的性质



### 3.2 node2vec

* design a flexible neighborhood sampling strategy: smoothly interporlate between BFS and DFS

#### 3.2.1 Random Walks

* $[c_0, c_1, \cdots, c_l]$: 一条长度为$l$的路
* $\pi_{vx}$: 从结点$v$到$x$的转移概率（未标准化的）
* 给定起始点$c_0=u$, 则通过${\rm Pr}(c_i =x \mid c_{i-1}=v) = \begin{cases} \frac{\pi_{vx}}{Z} & (v, x) \in E \\ 0 & otherwise \end{cases}$

#### 3.2.2 Search bias $\alpha$

* 我们可以直接用边的权重$w_{vx}$来代表$\pi_{vx}$，

  * 但是这不能代表我们网络的结构，并且不能来探索不同的近邻结点
  * 希望我们的搜索策略能同时包含不同的等价性，而不是相互排斥

* 引入两个参数$p, q$来指导路（walk）的产生，实际上是一个2阶马尔可夫过程

  * $v$是当前结点，$t$是上一步的结点，$x$是下一步的结点，$d_{tx}$表示两节点的最短距离

  $$
  \pi_{vx}=\alpha_{pq}(t, x) . w_{vx} \\
  \alpha_{pq}(t, x) = 
  \begin{cases} 
  \frac{1}{p} & d_{tx}=0 \\ 
  1 & d_{tx}=1 \\ 
  \frac{1}{q} & d_{tx}=2 \\ 
  \end{cases}
  $$

* return parameter $p$:
  * high value,：避免重复访问
  * low value：相当于后退了一步，使walk徘徊在结点$u$的附近
* In-out parameter $q$:
  * q>1：结点更倾向于接近于$t$的结点，类似BFS
  * q<1：远离$t$结点，类似DFS



#### 3.2.3 algorithm

![image-20220511132239625](C:\Users\86188\AppData\Roaming\Typora\typora-user-images\image-20220511132239625.png)

* alias sampling





































