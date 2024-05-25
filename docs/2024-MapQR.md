---
share: true
---

# Problem

- 之前的在线建图算法在Query的设计机制上考虑得不够；
- 之前的算法都考虑point-query，没办法表示一整个地图元素的位置和语义信息；甚至会出现同一个地图元素的不同的点的语义类别不同；
- 现在的方案在考虑query的时候没有考虑位置，只是随机初始化；

# Concept

- 使用instance query，而不是point query，提高一致性；

##  Scatter-and-gather query

- 使用instance query
- 使用reference point来生成位置编码

# Architecture

- 

# Code

- https://github.com/HXMap/MapQR