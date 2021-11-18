# 1.2 Important Network

* **Simple/Regular Network**: regular connections among node
  * two-dimensional lattice: Ising model
  * linear chains
  * non-rectangular lattices: protein structure
* **(Poisson) Random Network** / E-R Network: 两个节点之间以概率p相连
  * expected number of edges: $E(|e|) = p \times \frac{N(N-1)}{2}$
  * degree distribution: $P(v_i = k) = {N-1 \choose k} p^k (1-p)^{N-1-k}, k=0, \cdots, N-1$
    * when $N \rightarrow \infty, \lambda = p(N-1)$, $P(v_i = k) = \frac{\lambda^k}{k!}\exp(-\lambda)$
    * 由于每个节点的等价性，这个分布也可以看作是整个网络的度分布
* **Small-world Network** (Watts and Strogatz):

* **Scale-Free Network** / B-A model / preferential attachment model: $P(k) \sim k^{-\gamma}$
  * degree distribution following a power law
  * 节点生长模型，度高的节点更倾向于发生连接
* Trees: no loop in graph
* Generalized Trees: 为每个顶点设置了level set $L$ 以及multilevel function $\cal{L}_i$
  * 允许同一个水平以及不同水平的连接



# 1.3 Structural Network Analysis

## Important measure

* degree distribution
  * Poisson distribution
  * power law distribution
* clustering coefficient: i节点的近邻发生连接的概率
  * for node $i$, $C_i = \frac{2E_i}{k_i(k_i-1)}$, $E_i$是与近邻连接的边数
    * i节点的近邻发生连接的概率
* Path-Based measures
  * shortest distance for between node $d(v_i, v_j)$
    * measures for the node $v_i$
      * j-Sphere: a set $S_j(v_i, G) = {v \in V \mid d(v_i, v)=j, j\geq 1}$, $|S_j(v_i, G)|$
      * Eccentricity $\sigma(v_i) = \max_\limits{u \in V} d(u, v_i)$
    * measures for the whole network $G$
      * mean distance
      * Diameter $\rho(G) = \max_\limits{v \in V} \sigma(v) = \max d(v_i, v_j)$
      * radius $r(G) = \min_\limits{v \in V} \sigma(v)$
  * degree $k(v_i)$:
    * degree of the network $k(G) = \sum_\limits{v \in V} \frac{k(v_i)}{N}$
    * variance of the degree $Var(G) = \frac{1}{N-1} \sum_\limits{v_i \in V} (k(v_i) - k(G))^2$
    * mean of absolute distance $\tau(G) = \frac{1}{N} \sum_\limits{v_i \in V} |k(v_i) - k(G)|$
    * edge density $\beta(G) = \frac{|E|}{{N \choose 2}}$

* Centrality measures: detect important vertices

  * point centrality

    * degree centrality: $C_D(v_i) = k(v_i)$
    * betweenness centrality: $C_B(v_i) = \sum_\limits{v_k, v_j \in V \\ v_k \not=v_j} \frac{\sigma_{v_kv_j}(v_i)}{\sigma_{v_kv_j}}$
      * $v_k, v_j$之间的最短路径数，最短路径中包含$v_i$的数量
      * $v_i$在$v_i, v_j$最短路径上的概率
      * 用于分析通讯网络
    * Closeness centrality $C_c(v_i) = \frac{1}{\sum_{k=1}^Nd(v_k, v_i)}$
      * 一个节点与其他节点的距离

  * graph centrality
    $$
    \begin{cases}
    C_x &= \frac{\sum_{i=1}^N C_x(v_\max) - C_x(v_i)}{C_x^{\max}} \\
    C_x(v_\max) &= \max_\limits{i \in V} C_x(v_i) \\
    C_x^{\max} &= \max_\limits{G \in \mathcal{G}(N)} \sum_\limits{i=1}^N C_x(v_\max) - C_x(v_i)
    \end{cases}
    $$

  * Extended Centrality Measure
    * eigenvector centrality $C_e = \xi_\max = \frac{1}{\lambda_\max}A\xi_\max$
      * 重要的顶点与重要的近邻相连
    * joint betweenness centrality $C_{JB}(v_i, v_j) = \sum_\limits{v_k, v_l \in V \\ v_k \not=v_l} \frac{\sigma_{v_kv_l}(v_i, v_j)}{\sigma_{v_kv_l}}$
      * different normalization: $C_{JB}(v_i, v_j) = \sum_\limits{v_k, v_l \in V \\ v_k \not=v_l} \frac{\sigma_{v_kv_l}(v_i, v_j)}{\sigma_\max}, \sigma_\max = \max_\limits{v_i, v_j} \sigma_{v_i, v_j}$

## Comparative analysis





















