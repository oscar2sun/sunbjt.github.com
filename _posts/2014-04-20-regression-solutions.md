--- 
layout: post
title: 回归的几种实现方法
tags: 
- bigdata
- business
status: publish
type: post
published: false
---

回归（regression）估计是所有统计系的入门的必学课程之一，一般课本中出现的方法无外乎就是 $\beta$ 的求解，

在 $X$ 是满秩的条件下，最小二乘的估计为

$$\hat{\beta}_{ols} = (X^T X)^{-1}X^T y$$

直接计算 $(X^T X)^{-1}$ 会存在一些稳定性问题，因此一般使用的矩阵正交分解的技术（尽管可能会更慢），比如 QR 分解。


# 为什么要使用R语言

在互联网企业，在分析端使用闭源的商用软件几乎是不可能的，原因很简单：成本太高，不管是使用，还是研发及维护。
但我个人觉得这可能还不是最主要的原因，对于互联网企业来说，数据虽然获取更容易，但环境更为复杂。开源软件可以根据业务的变化
进行调整，但商业的闭源软件则很难做到。

再说个例子：试问100万行，20万列的数据是大数据么（你没看错，是200000维）？恩，R能够处理，而且可以在这类数据上构建模型。
