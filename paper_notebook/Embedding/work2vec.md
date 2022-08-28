# Distributed Representations of Words and Phrases and their Compositionality

## Abstract

* Subsampling of frequent words / negative sampling
  * **more regular** word representations
  * significant speed up
* a simple method for finding phrases



## 1. Introduction

* A brief history of **Distributed representations of words**
  * earliest 1986, Rumelhart, Hinton, and Williams
  * statistical language modeling
  * apply to other NLP tasks

* **Skip-gram model**: Efficent
  * 中心词预测临近词
  * Not involve dense **matrix multiplications**

* Word representations
  * explicitly encode many linguistic regularities and patterns
* Several extensions of the original Skip-gram model
  * Subsampling of frequence words
    * significant speed up (2x - 10x)
    * improve accuracy of the representations of **less frequent words**
  * a simplified variant of **Noise Contrastive Estimation** (NCE) for training Skip-gram model
    * compare to **hierarchical softmax**
    * better vector representations for **frequent words**
    * faster training
  * phrase representation: 采用数据驱动的方式鉴定phrase，并把这个整体当作一个token
    * 在test set上表现良好
* interesting property: 对词向量的线性叠加可以可以产生有意义的结果



## 2 Skip-gram Model

* objective function: $\frac{1}{T} \sum\limits_{t=1}^T \sum\limits_{-c \leq j \leq c, j \not= 0} \log p(w_{t+j} \mid w_t)$
  * $p(w_{O} \mid w_I) = \frac{\exp({v'_{w_O}}^T v_{w_I})}{\sum_\limits{w=1}^W \exp({v'_{w_O}}^T v_{w_I})}$, **computing cost**



### 2.1 Hierarchical softmax

* 只对$\log_2 W$ 个输出节点做估计
* Notation：
  * $n(w, j)$: 从root到词$w$（leaf）的第j个节点
  * $L(w)$: 从root到词$w$的路径长度
  * ${\rm ch}(n) $: 节点$n$的子节点
  * $\delta(x) = \begin{cases} 1 & x =true \\ -1 & otherwise \end{cases}$
  * 



























