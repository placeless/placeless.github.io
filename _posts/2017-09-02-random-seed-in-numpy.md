---
layout: post
title:  "numpy.random.seed()"
date:   2017-09-02 05:44 +0800
---

看《Python Data Science Handbook》[第二章](https://jakevdp.github.io/PythonDataScienceHandbook/02.02-the-basics-of-numpy-arrays.html)遇到一个疑问。

书中提到：

```python
import numpy as np
np.random.seed(0)

x1 = np.random.randint(10, size=6)
x2 = np.random.randint(10, size=(3, 4))
x3 = np.random.randint(10, size=(3, 4, 5))
```

在使用 numpy 产生 3 个随机列表之前有一行 `np.random.seed(0)`我想知道它具体是怎么工作的。

`help(np.random.seed)` ，讲了怎么使用，但没有说明它是什么，为什么会这么设计。之后在 SO 上找到了[答案](https://stackoverflow.com/questions/21494489/what-does-numpy-random-seed0-do)，简言之：

- 机器通过特定公式来产出伪随机数（pseudo-random）
- 这个公式需要一个初始输入（seed）来产生第一个结果
- 前面的结果会作为后面的 seed，源源不断

固定了 seed（输入），也就固定了结果（输出）。也就是说，seed 是用来重现同一组随机数的。

因为如果不设定 seed，系统也会从 `/dev/urandom` 或者时钟里面取数作为 seed，这样得出的随机数显然每次都是不一样的。但问题来了，随机数不就应该随机吗，为什么还要重现它？答案是，比如，生成了一组随机数来做测试，在 debug 时，还需用到这一组随机数来复现问题，这种情况下，测试初始设定一个 seed 就很有用了，因为按照这一 seed 产出的随机数肯定都是一样的。

