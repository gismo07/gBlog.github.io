---
layout: post
title:  "Learning Rate Warmup"
date:   2020-10-12 10:11:37 +0200
comments: false
tags: DeepLearning Pytorch
author: "Johann"
---

$$ \eta(epoch) = \lambda(epoch) * \eta_0 $$


## linear

$$\lambda(epoch) = \min\left (1,\frac{epoch + 1}{\tau + 1}\right )$$

## exponential

$$ \lambda(epoch) = 1 - \exp\left ( -1\frac{(epoch +1)}{\tau + 1}  \right ) $$

<!-- ```python
lambdaLinear = lambda epoch: np.minimum(1.0, (epoch + 1) / (TAU + 1))
``` -->


![linear vs. exponential warmup]({{site.baseurl}}/assets/lrw/learningRateWarmup.jpeg)
*Comparison of linear and exponential warmup. The warmup time window $$\tau$$ was set to 5.*