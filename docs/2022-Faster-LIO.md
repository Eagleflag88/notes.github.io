---
share: true
---

# Sensor

- IMU
- Lidar

# Problem
- 现在的方法比较慢，比如说LOAM，BALM，消耗100ms；

- Mapping Structure
	- voxel:  easy to construct and delete almost in constant time but are incapable of strict k-NN search
	- k-d tree: can provide strict k-NN search and range search results but require extra efforts to swing and balance the tree.

# Approach

- 基本结构参考[[2021-Fast-LIO2#Approach|2021-Fast-LIO2 > Approach]]，主要修改了Map的数据结构；

## iVOX

- Structure
	- 点云被存在稀疏的voxel中，每个voxel至少包含一个点；
	- voxel的index存在hash map中
	- 里面的点云可以有linear和pHC(pseudo-Hilbert curve)两种保存形式
- kNN search
	- 快速
	- 结果不保证一定对，但是足够对；
- Map Update
	- Insertion
	- Deletion: LRU Cache-based

# Code

- http://github.com/gaoxiang12/faster-lio

# Ref

- Faster-LIO: Lightweight Tightly Coupled Lidar-Inertial Odometry Using Parallel Sparse Incremental Voxels