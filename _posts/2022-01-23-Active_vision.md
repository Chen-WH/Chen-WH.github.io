---
layout:     post
title:      Active vision connectivity maintenance
subtitle:   主动视觉连通保持
date:       2022-01-23
author:     ChenWH 
header-img: img/IcelandBonfire.jpg
catalog:   true
tags:

    - Robotics
---

# 主动视觉连通保持

## 集中式

### 权重函数

$$
w_{ij}=c_1\exp\left[-(\psi_{ij}-\varphi_i)^2/\sigma_1^2\right]+c_2\exp(-\varphi_i^2/\sigma_2^2)+c_3\exp\left[-c_3(|\theta_j-\theta_i|-\frac{\pi}{2})^2/\sigma_3^2\right]
$$

### 拉普拉斯矩阵

$$
\begin{aligned}
    \widetilde{w}_{ij}&=w_{ij}+w_{ji}\\
    &=c_1\exp\left[-(\psi_{ij}-\varphi_i)^2/\sigma_1^2\right]+c_2\exp(-\varphi_i^2/\sigma_2^2)+c_3\exp\left[-c_3(|\theta_j-\theta_i|-\frac{\pi}{2})^2/\sigma_3^2\right]\\
    &+c_1\exp\left[-(\psi_{ji}-\varphi_j)^2/\sigma_1^2\right]+c_2\exp(-\varphi_j^2/\sigma_2^2)+c_3\exp\left[-c_3(|\theta_j-\theta_i|-\frac{\pi}{2})^2/\sigma_3^2\right]\\
\end{aligned}
$$

$$
\begin{aligned}
L&=\left[\begin{array}{cccc}
\widetilde{w}_{12}+\widetilde{w}_{13}+\widetilde{w}_{14} & -\widetilde{w}_{12} & -\widetilde{w}_{13} & -\widetilde{w}_{14} \\
-\widetilde{w}_{12} & \widetilde{w}_{12}+\widetilde{w}_{23}+\widetilde{w}_{24} & -\widetilde{w}_{23} & -\widetilde{w}_{24} \\
-\widetilde{w}_{13} & -\widetilde{w}_{23} & \widetilde{w}_{13}+\widetilde{w}_{23}+\widetilde{w}_{34} & -\widetilde{w}_{34} \\
-\widetilde{w}_{14} & -\widetilde{w}_{24} & -\widetilde{w}_{34} & \widetilde{w}_{14}+\widetilde{w}_{24}+\widetilde{w}_{34} \\
\end{array}\right]\\
\end{aligned}
$$

### 控制律

$$
u_i^\theta=\frac{\partial\lambda_2}{\partial \theta_i}\\
u_i^\varphi=\frac{\partial\lambda_2}{\partial \varphi_i}\\
$$

## 双积分系统

$$
u_i=\dot{p}_i=\frac{\partial \lambda_2}{\partial p_i}\\
\dot{u}_i=\ddot{p}_i=\frac{\partial}{\partial t}(\frac{\partial \lambda_2}{\partial p_i})=\frac{\partial}{\partial p_i}(\frac{\partial \lambda_2}{\partial p_i})\cdot\frac{\partial p_i}{\partial t}=\frac{\partial}{\partial p_i}(\frac{\partial \lambda_2}{\partial p_i})u_i=\frac{\partial u_i}{\partial p_i}u_i\\
$$

$$
u_i^\theta=\frac{\partial \lambda_2}{\partial \theta_i}\\
\dot{u}_i^\theta=\frac{\partial u_i^\theta}{\partial \theta_i}u_i^\theta=\frac{\partial^2\lambda_2}{\partial \theta_i^2}\cdot\frac{\partial \lambda_2}{\partial \theta_i}\\
u_i^\varphi=\frac{\partial \lambda_2}{\partial \varphi_i}\\
\dot{u}_i^\varphi=\frac{\partial u_i^\varphi}{\partial \varphi_i}u_i^\varphi=\frac{\partial^2\lambda_2}{\partial \varphi_i^2}\cdot\frac{\partial \lambda_2}{\partial \varphi_i}\\
$$

## 分布式

$$
u_i=\frac{\partial\lambda_2}{\partial p_i}=v_2^T\frac{\partial L}{\partial p_i}v_2
$$

### 幂迭代

$$
(1) & \dot{\boldsymbol{x}}=-\mathrm{Ave}(\boldsymbol{x})\boldsymbol{1}\\
(2) & \dot{\boldsymbol{x}}=-\alpha\boldsymbol{Lx}\\
(3) & \dot{\boldsymbol{x}}=-[\mathrm{Ave}(\boldsymbol{x}\odot\boldsymbol{x})-1]\boldsymbol{x}\\
$$

第一步使 $\boldsymbol{x}$ 趋向 $\boldsymbol{1}$ 的零空间，第二步使 $\boldsymbol{x}$ 趋向 $\boldsymbol{v}_2$ ，第三步使 $\boldsymbol{x}$ 趋向半径 $\sqrt{n}$ 的球体（这一步可以用除法单位化代替）。

构造矩阵 $\boldsymbol{B}=\boldsymbol{I}-\alpha\boldsymbol{L}$ 用于迭代，只要 $\alpha$ 足够小，即可保证 $\boldsymbol{B}$ 特征值 $1-\alpha\lambda_1>1-\alpha\lambda_2>\cdots>0$

已知拉普拉斯矩阵最小特征值为0，对应特征向量为 $\boldsymbol{1}$
$$
\boldsymbol{x^T1}=0\\
\left(\sum_{i=1}^n\alpha_i\boldsymbol{v}_i\right)^T\boldsymbol{1}=0\\
\left(\alpha_1\boldsymbol{1}+\sum_{i=2}^n\alpha_i\boldsymbol{v}_i\right)^T\boldsymbol{1}=0\\
\alpha_1\boldsymbol{1}^T\boldsymbol{1}=0\\
\alpha_1=0\\
$$
由幂迭代(Power Iteration)可得此时迭代得到特征向量 $\boldsymbol{v}_2$，实际分布式实现中，可以通过一致性得到 $\mathrm{Ave}(\boldsymbol{x}),\mathrm{Ave}(\boldsymbol{x}\odot\boldsymbol{x})$，但需要提高通讯频率，实测通讯频率为控制频率5~10倍时有较好结果。

