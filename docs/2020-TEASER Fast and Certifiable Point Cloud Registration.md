---
share: true
---
# Approach
求解点云配准问题：
1. 使用Truncated Least Squares (TLS)作为robust loss；
2. Rotation的估计使用semidefinite program (SDP)来松凸优化问题
3. 使用graph-theoretic framework to allow drastic pruning of outliers by finding the maximum clique.

# Teaser++
- 由于SDP太慢，使用graduated non-convexity来求解问题，即teaser++算法

# Code
https://github.com/MIT-SPARK/TEASER-plusplus