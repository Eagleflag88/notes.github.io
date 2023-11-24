---
share: true
---
# Structure
- Variable Node: State → All possible variable
- Factor Node：
	- Item of factorization in Ref eq 7
	- Error Function to be minimized
	- 一般是马氏距离
		- 优化的目标函数需要白化，所以乘上了$COV^{-1}(Error)$
		- 所以Factor graph使用的是generalized least square问题的框架
- Edge: Connection between variable and factor node
	
# Factors
- Prior Factor: Error between state and the prior distribution → $Err = ||x - x_0||^2$
- IMU Factor: Error between $x_{k+1}$ and the predicted value using $x_k, h(x_k)$
	- $Err = ||x_{k+1} - h(x_k)||^2$
	- $h(x_k)$ is integration using IMU measurement
- GPS Factor: Error between GPS measurement $z_k$ and the ego pose $x_k$ → $Err = ||x_k - z_k||^2$
- Visual Factor
	- Projection of Landmark, L onto the image plane creating a observation, z → $Err = ||z - proj(x, L)||^2$
	- The same Landmark can be observed by multiple camera poses
	
# Inference

# Ref
- Information Fusion in Navigation Systems via Factor Graph Based Incremental Smoothing.pdf