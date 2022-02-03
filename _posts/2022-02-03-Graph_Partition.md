---
layout:     post
title:      Spectral Clustering
subtitle:   图划分/谱聚类
date:       2022-02-03
author:     ChenWH
header-img: img/FortCorjuem.jpg
catalog:   true
tags:

    - Swarm
---

# Graph Partition

图 $V$ 的 $k$ 划分 $\mathcal{P}=\{\boldsymbol{V}_1,\boldsymbol{V}_2,\dots,\boldsymbol{V}_k\}$ 对应的关联矩阵 $\prod=[\pi]_{vj}$ 定义如下
$$
\pi_{vj}=
\left\{\begin{array}{ll}
&1 & v\in V_j\\
&0 & v\not\in V_j\\
\end{array}\right.\quad
v\in V
$$

## 定义切图

$$
切图权重W(\boldsymbol{A},\boldsymbol{B})=\sum_{i\in\boldsymbol{A},j\in\boldsymbol{B}}w_{ij}\\
\mathrm{cut}(\boldsymbol{V}_1,\boldsymbol{V}_2,\dots,\boldsymbol{V}_k)=\frac{1}{2}\sum_{i=1}^kW(\boldsymbol{V}_i,\overline{\boldsymbol{V}}_i)
$$

为了规避最小切的出现，需要对每个子图的规模做出限定

## RatioCut

$$
\mathrm{RatioCut}(\boldsymbol{V}_1,\boldsymbol{V}_2,\dots,\boldsymbol{V}_k)=\frac{1}{2}\sum_{i=1}^k\frac{W(\boldsymbol{V}_i,\overline{\boldsymbol{V}}_i)}{|\boldsymbol{V}_i|}
$$

引入指示向量
$$
h_{ij}=\left\{\begin{array}{ll}
&0 & v_i\in V_j\\
&\frac{1}{\sqrt{|\boldsymbol{V}_j|}} & v_i\not\in V_j\\
\end{array}\right.\\
\boldsymbol{h}_i^T\boldsymbol{L}\boldsymbol{h}_i=\frac{1}{2}\sum_{j,k=1}^nw_{jk}(h_{ij}-h_{ik})^2=\frac{W(\boldsymbol{V}_i,\overline{\boldsymbol{V}}_i)}{|\boldsymbol{V}_i|}\\
\mathrm{RatioCut}(\boldsymbol{V}_1,\boldsymbol{V}_2,\dots,\boldsymbol{V}_k)=\frac{1}{2}\sum_{i=1}^k\boldsymbol{h}_i^T\boldsymbol{L}\boldsymbol{h}_i=\frac{1}{2}\sum_{i=1}^k(\boldsymbol{H}^T\boldsymbol{L}\boldsymbol{H})_{ii}\\
=\frac{1}{2}\tr(\boldsymbol{H}^T\boldsymbol{L}\boldsymbol{H})
$$
注意到 $\boldsymbol{H}^T\boldsymbol{H}=\boldsymbol{I}$ 故而 $\boldsymbol{h}_i^T\boldsymbol{L}\boldsymbol{h}_i$ 均为 $\boldsymbol{L}$ 的特征值，寻找最小的 $k$ 个特征值后求取对应特征向量即为 $\boldsymbol{H}$ 

由于我们在使用维度规约的时候损失了少量信息，导致得到的优化后的指示向量 $\boldsymbol{h}$ 对应的 $\boldsymbol{H}$ 现在不能完全指示各样本的归属，因此一般在得到 $n\times k$ 维度的矩阵 $\boldsymbol{H}$ 后还需要对每一行进行一次传统的聚类，比如使用 K-Means 聚类.

## Ncut

将 $1/\sqrt{|\boldsymbol{V}_j|}$ 更换为基于权重的 $1/\sqrt{vol(\boldsymbol{V}_j)}$

但此时 $\boldsymbol{H}^T\boldsymbol{H}\not=\boldsymbol{I},\boldsymbol{H}^T\boldsymbol{DH}=\boldsymbol{I}$ 

构造 $\boldsymbol{H}=\boldsymbol{D}^{-1/2}\boldsymbol{F}$

## Reference

- https://www.cnblogs.com/pinard/p/6221564.html
