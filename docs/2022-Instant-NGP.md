---
share: true
---

# Problem
- 直接用Nerf的方法，需要太多次query MLP，时间上耗不起；

# Potential Solution
- 在超/空间中的Grid点上面直接存储相关特征向量，同时，原始的MLP被大幅度的缩小。这样的方法有如下好处：
	1. 训练的时候因为可以知道一次参数更新的空间范围，可以只更新在涉及范围内的参数；
	2. 推理的时候可以直接内插获得结果，不需要query MLP；
- 但是也有缺点→不够自适应，在空的地方也存储大量特征向量，只有很少一部分可见表面被编码了；

# Approach

- Multiresolution hash encoding: 
	1. Store trainable feature vectors in a compact spatial hash table;
	2. We use multiple separate hash tables indexed at different resolutions, whose interpolated outputs are concatenated before being passed through the MLP；
- 把输入x按照多个尺度分成L层；
- 每层按照各自的尺度分成网格，网格的顶点储存一个特征向量F；这个F通过hash table存储起来；

# Process
- Scaling：把输入按照相应的尺度进行变换；
- 根据x的位置找到它所在的网格的顶点；
- 利用hash table来查找它周围顶点的特征值；
- Lastly, the feature vectors at each corner are d-linearly interpolated according to the relative position of x within its hypercube；
- 各层的结果concat到一起，然后输入到MLP；

# Ref
- Original Paper: Instant Neural Graphics Primitives with a Multiresolution Hash Encoding
- [知乎详解](https://zhuanlan.zhihu.com/p/631284285)