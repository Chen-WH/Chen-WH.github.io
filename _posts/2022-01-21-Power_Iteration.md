---
layout:     post
title:      Power Iteration
subtitle:   幂迭代算法
date:       2022-01-21
author:     ChenWH 
header-img: img/MeotoIwa.jpg
catalog:   true
tags:

    - Math
---

# Power Iteration

## 基本定义

对于矩阵 $\boldsymbol{A}$，其特征值为 $\lambda_i$，对应特征向量 $\boldsymbol{v}_i$

任取 $\boldsymbol{x}_0$ 构造 $\boldsymbol{x}_k=\boldsymbol{A}^k\boldsymbol{x}_0$，前 $r$ 个（一般情况下 $r=1$）为绝对值最大的特征值

$$
\begin{aligned}
|\lambda_1|=|\lambda_2|=\cdots=|\lambda_r|>|\lambda_{r+1}|\geq\cdots\geq|\lambda_n|
\end{aligned}
$$

矩阵的特征向量构成一组基


$$
\begin{aligned}
\boldsymbol{x}_0&=\sum_{i=1}^n\alpha_i\boldsymbol{v}_i\\
\boldsymbol{x}_k&=\boldsymbol{A}^k\boldsymbol{x}_0=\sum_{i=1}^n\boldsymbol{A}^k\alpha_i\boldsymbol{v}_i\\
\boldsymbol{x}_k&=\sum_{i=1}^n\alpha_i\lambda_i^k\boldsymbol{v}_i=\lambda_1^k\left[\sum_{i=1}^r\alpha_i\boldsymbol{v}_i+\sum_{i=r+1}^n\left(\frac{\lambda_i}{\lambda_1}\right)^k\alpha_i\boldsymbol{v}_i \right]\\
\lim_{k\to\infty}(\frac{\lambda_i}{\lambda_1})^k&=0(i=r+1,\dots,n)\\
\lim_{k\to\infty}\boldsymbol{x}_k&=\lim_{k\to\infty}\lambda_1^k\left(\sum_{i=1}^r\alpha_i\boldsymbol{v}_i \right)\\
\end{aligned}
$$


因为 $\left(\sum_{i=1}^r\alpha_i\boldsymbol{v}_i \right)$ 是固定项


$$
\begin{aligned}
\lim_{k\to\infty}\frac{(\boldsymbol{x}_{k+1})_i}{(\boldsymbol{x}_k)_i}=\lambda_1
\end{aligned}
$$


其中收敛速度由比值 $\lvert\lambda_{r+1}/\lambda_1\rvert$ 决定，越小收敛越快。

## 幂法改进

以上定义中，随着不断的迭代，如果 $|\lambda_1|>1$ ， $\boldsymbol{x}_k$ 会无限大；如果 $|\lambda_1|<1$ ， $\boldsymbol{x}_k$ 则会趋于0。如果 $|\lambda_{r+1}/\lambda_1|$ 很小，使得能快速收敛也无需优化。

但是通常不会碰到那么好的运气，所以就在每次迭代的时候就要对 $\boldsymbol{x}_k$ 进行单位化，防止它溢出。
$$
\begin{aligned}
|\lambda_1|=\lim_{k\to\infty}\frac{||\boldsymbol{x}_{k+1}||}{||\boldsymbol{x}_k||}
\end{aligned}
$$
k足够大时，每次迭代各分量正负号不变，则 $\lambda_1$ 为正
$$
\begin{aligned}
\boldsymbol{v}_1=\lim_{k\to\infty}\boldsymbol{x}_k=\lim_{k\to\infty}\boldsymbol{A}^k\boldsymbol{x}_0
\end{aligned}
$$
可获得一个单位化的主特征值特征向量。因为还乘上了 $\lambda_1^k$ 的符号，所以向量各个分量的符号和迭代的次数 k 有关。$\alpha_1=0$ 则迭代出第二大特征值/对应特征向量

## 迭代加速

$$
\begin{aligned}
\boldsymbol{B}=\boldsymbol{A}-p\boldsymbol{I}\\
\end{aligned}
$$



利用 $\boldsymbol{B}$ 迭代即可得到其特征值 $\lambda_1-p,\lambda_2-p,\dots$ 中绝对值最大者，可以调整迭代比值 $|\lambda_{r+1}/\lambda_1|$ 及目标特征值/特征向量

## 参考资料

- https://www.cnblogs.com/qizhou/p/12271287.html
