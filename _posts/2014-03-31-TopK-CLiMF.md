r--- 
layout: post
title: CLiMF: Learning to Maximize Reciprocal Rank with collaborative Less-is-More Filtering
tags: 
- cf
- CLiMF
status: publish
type: post
published: false
---

Top K 推荐问题是我们经常看到的推荐场景，

# 为什么要使用R语言

在互联网企业，在分析端使用闭源的商用软件几乎是不可能的，原因很简单：成本太高，不管是使用，还是研发及维护。
但我个人觉得这可能还不是最主要的原因，对于互联网企业来说，数据虽然获取更容易，但环境更为复杂。开源软件可以根据业务的变化
进行调整，但商业的闭源软件则很难做到。

再说个例子：试问100万行，20万列的数据是大数据么（你没看错，是200000维）？恩，R能够处理，而且可以在这类数据上构建模型。

# 再看看大数据的流动

```r
i <- match(x$V1, unique(x$V1))
j <- match(x$V2, unique(x$V2))
library(Matrix)
(A <- sparseMatrix(i, j, x = 1))
t(A) %*% A
diag(A)
z <- t(A) %*% A
m <- Matrix(0, 5,5 )
diag(m) <- 1/diag(z)
m
m %*% z %*% m
```
