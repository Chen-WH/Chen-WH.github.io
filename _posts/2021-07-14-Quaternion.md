---
layout:     post
title:      Quaternion
subtitle:   (副标题) 
date:       2021-07-17
author:     ChenWH
header-img: img/the-first.png
catalog:   true
tags:

    - Math
---



<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# 四元数与空间旋转

## 欧拉角

![欧拉角](20151125212621964.png)

Roll桶滚，Pitch俯仰，Yaw偏航

## 万向节死锁

![v2-78acd2f7074ed777c9b18e37d77252f6_720w](v2-78acd2f7074ed777c9b18e37d77252f6_720w.jpg)

（图示为万向节）当俯角为±90时，整个旋转表示系统被限制在只能绕竖直轴旋转，丢失了一个表示维度。称为万向节死锁，并不是说不能旋转了，而是会导致旋转不自然。要规避万向节死锁，需要选择合适的旋转顺序。在编程中很难规避死锁问题，所以需要引入四元数

## 四元数

参考资料：https://github.com/Krasjet/quaternion         文档版：[quaternion.pdf](quaternion.pdf) 

具体证明推导过程参见上方资料，下面仅记录阶段性的结论。速记要点：

1. 四元数虚部不同部分之间计算遵从叉乘，其他均遵从复数。
2. 若 $q=[\cos\theta,\sin\theta\boldsymbol{u}]$​，那么 $v'=qvq^{-1}$​ 可以将 $\boldsymbol{v}$​ 沿着 $\boldsymbol{u}$​​ 旋转 $2\theta$​
3. $q_1^{-1}q_2^{-1}=(q_2q_1)^{-1}$

$$
\begin{aligned}
	&3D旋转公式（向量型），\boldsymbol{v}沿\boldsymbol{u}旋转\theta之后& &\boldsymbol{v'}=\cos\theta\boldsymbol{v}+(1-\cos\theta)(\boldsymbol{u}\cdot\boldsymbol{v})\boldsymbol{u}+\sin\theta(\boldsymbol{u}\cross\boldsymbol{v})\\
	&Graßmann积，q_1=[s,\boldsymbol{v}]，q_2=[t,\boldsymbol{u}]& &q_1q_2=[st-\boldsymbol{v}\cdot\boldsymbol{u},s\boldsymbol{u}+t\boldsymbol{v}+\boldsymbol{v}\cross\boldsymbol{u}]\\
	&3D旋转公式（四元数型）& &v'=qvq^{-1}=[0,\cos\theta\boldsymbol{v}+(1-\cos\theta)(\boldsymbol{u}\cdot\boldsymbol{v})\boldsymbol{u}+\sin\theta(\boldsymbol{u}\cross\boldsymbol{v})]\\
	&3D旋转公式（矩阵形式）& &
	q v q^{*}=\left[\begin{array}{cccc}
	1 & 0 & 0 & 0 \\
	0 & 1-2 c^{2}-2 d^{2} & 2 b c-2 a d & 2 a c+2 b d \\
	0 & 2 b c+2 a d & 1-2 b^{2}-2 d^{2} & 2 c d-2 a b \\
	0 & 2 b d-2 a c & 2 a b+2 c d & 1-2 b^{2}-2 c^{2}
	\end{array}\right] v\\

\end{aligned}
$$

