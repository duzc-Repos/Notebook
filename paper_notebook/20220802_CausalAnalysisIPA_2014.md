# Causal analysis approaches in Ingenuity Pathway Analysis



## Method

### 1. Causal network

* $G = (V, E)$: directied multigraph （两个结点可能连接不同类型的边）
  * $v \in V$: representing genes, chemicals, protein families, complexes, microRNA, biological processes, etc.
    * $V_g \subset V$: the set of all genes
    * $V_p \in V$: the set of all biological processes
  * $e \in E$: associated with a set of underlying findings $F(e)$
    * $\forall f \in F(e), \hat{s}(f) \in \{-1, 0, 1\}$: 代表因果效应的调控方向(inhibition, unknown, activation)
    * $\sigma(e), \tau(e)$: the source and target node of edge $e$
    * $E_T \subset E$: 转录或表达相关的效应，e.g. protein-DNA相互作用，改变转录丰度
    * $E_A \subset E$: functional activation or inhibition of target node (e.g. 通过磷酸化或信号级联)
    * $E_P \subset E$: regulation of biological processes (e.g. 凋亡)
    * $s(e) = {\rm sign}(\sum_\limits{f \in F(e)} \hat{s}(f) )$: unique direction of causal effect
    * $w(e) = \frac{1}{N+1} \left| \sum_\limits{f \in F(e)} \hat{s}(f) \right| $: confidance in the assigned direction of causal effect
  * association between $v$ and $e$
    * $\forall e \in E_T \Rightarrow \tau(e) \in V_g$
    * $\forall e \in E_P \Rightarrow \tau(e) \in V_p$



### 2. Gene-expression data

* $d \in D$: 利用差异表达基因
  * $s_D(d) \in {-1, 1}$: 表示基因上调还是下调



### 3. Upstream Regulator Analysis (URA)

* $G'=(V, E_t)$: 仅考虑跟转录和表达相关联的边
  * $V_{rg} := \{ v \in V_g \mid \exists e \in E_T, v=\tau(e) \}$: 至少被一条边调控的基因
  * $R_r := \{ v \in V_{rg} \mid \exist e \in E_T, r=\sigma(e), v=\tau(e) \}$: potential regulator
  * $s_R(r, v) := s(e)\ (r=\sigma(e), v=\tau(e))$: 假定$r$激活的情况下对$v$的调控方向
  * $w_R(r, v) := w(w)\ (r=\sigma(e), v=\tau(e))$

* **Overlap P-value** (Fisher exact test)

  Given particular regulator $r$
  $$
  P(r) = \sum_\limits{k=0}^{min(c, d)} \frac{(a+b)!(c+d)!(a+c)!(b+d)!}{(a+k)!(b-k)!(c-k)!(d+k)!n!}
  $$

  * $n = |V_{rg}|$: universe

  * $a = O(r) = |R(r) \cap D|$: overlap

  * $b = |D \cap V_{rg}| - |O(r)|$

  * $c = |R(r)| - |O(r)|$

  * $d=n-a-b-c$

    |          | 调控子$r$的靶基因 | 非靶基因 |               |
    | -------- | ----------------- | -------- | ------------- |
    | 差异基因 | $a,|O(r)|$        | b        | $D$           |
    | 非差异   | $c$               | d        |               |
    |          | $|R(r)|$          |          | $n, |V_{rg}|$ |

* **Activation Z-score**

  * 作用：

    * 推断putative regulator的激活状态（activation or inhibition）
    * 确定可能的调控子

  * Notation

    * $N = \hat{O}(r) := \{v \in R(r) \cap D \mid s_R(r, v) \not=0 \}$
      $$
      z(r) = \frac{\sum_\limits{v \in \hat{O}(r)}w_R(r, v)s_R(r, v)s_D(v) }{ \left(\sum_\limits{v \in \hat{O}(r)} w_R(r, v) \right)^\frac{1}{2} }
      $$

    * 在$s_R(r, v)$和$s_D(v)$随机的条件下，$\lim_\limits{N \rightarrow \infty} z(r) = N(0,1)$

      > 假设$x_i \in \{-1, 1\} = s_R(r, v_i)s_D(v_i) \stackrel{i.i.d}{\sim} Be(0.5, 1) $, 则有$E(x_i) = 0, V(x_i)=1$
      >
      > 记$w_i = w_R(r, v_i), y=\sum_\limits{i} w_ix_i$, 则$E(y) = 0, V(y) = \sum_\limits{i} w_i^2 = \sigma(y)^2$
      >
      > 观察可知，$z(r) = \frac{y-E(y)}{\sigma(y)}$，得证

    * bais correction:

      * 一个更好的空模型应该是随机化数据，而不是随机化调控方向
      * 所以应该置换D中的labels，但是保持其调控方向的数量以及重合数量$N$不变，这会导致导致上述的分布发生skewing，可以人为设置一个非零的均值进行矫正，e.g. $u = \mu_D\mu_R = \frac{1}{|D|} \sum s_D(d) \frac{1}{N}\sum s_R(r, v_i)$























