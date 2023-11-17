---
share: true
---
# Preliminary
- minimal/non-minimal sovler: minimal sovler仅仅利用理论上最少的测量值，不利用冗余数据，minimal solver一般配合ransac使用；Non-minimal solver则一般通过构建最小二乘法来求解问题；
- 这些最小二乘问题一般对外点比较敏感，我们一般会给他加上一个robust loss，可以是convex的，比如说huber loss；也可以是non-convex robust loss，比如说Geman-McClure，Truncated Least Squares
- 但是convex loss对外点仍然敏感，break down点很低，所以我们需要求解一个non-convex的robust estimation problem；

# 目标
一个通用的non-convex的robust estimation problem solver；

# Process
- Graduated Non-Convexity Algorithm
- 通过引入robust cost function的替代函数逐步求解问题，这个替代函数由一个参数控制，随着参数变化，这个替代函数的会由convex变为non-convex也会变化；核心思想如下：
	1. 我们先使用一个convex的替代函数求解问题；更新替代函数；
	2. 然后利用求解出的解和新的替代函数，继续迭代求解；
	3. 迭代到最后，替代函数就会成为原函数，而解自然也是原问题的解；

# Code
https://github.com/MIT-SPARK/Kimera-RPGO.git
https://github.com/MIT-SPARK/GNC-and-ADAPT.git