# Slice-to-volume medical image registration: A survey

## 1. Introduction

$$
\hat \Theta = \arg \min\limits_{\Theta} \mathcal{M}(I, J; \Theta) + \mathcal{R}(\Theta)
$$

* $I$ : 2D slice
* $J$: 3D volume
* $\mathcal{M}$: (dis)similarity term / matching criterion
* $\mathcal{R}$ : regularization term



## 2. Matching criterion

* Iconic: monomodal or multimodal with more complex similarity measures
* geometric or sensor: multimodal
* hybrid



### 2.1 Iconic

$$
\delta(I_1, I_2): \Omega_1 \times \Omega_2 \rightarrow \mathbb{R}
$$

* Assuming straightforward corresponce between intensity values
  * sum of absolute differences (SAD): L1 norm
  * sum of square differences (SSD): L2 norm
  * mean of square differences (MSD)

* Exploiting statistical properties of intensity values
  * cross-correlation (CC)
  * correlation ratio (CR): $\eta(Y \mid X) = Var( E(Y \mid X) ) / Var(Y) $

* Information theoretic similarity measures
  * mutual information (MI): 需要两幅图像强度的**联合分布**和各自的**边缘分布**
    * 联合分布的估计是一个困难的问题
  * normalized mutual information (MNI)

* Using prior knowledge, e.g. segmentation masks
  * segmentation masks: Chan-Vese metric
  * Border information: 
* More robust similarity measures
  * Linear Correlation of Linear Combination ($LC^2$), $BOXLC^2$
  * modality independent neighborhood descriptor (MIND), Self Similarity Context (SSC)
  * Contextual Conditioned Mutual Information (CoCoMI)



### 2.2 Geometric



























