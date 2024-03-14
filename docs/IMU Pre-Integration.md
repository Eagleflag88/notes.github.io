---
share: true
---
# Measurement
- Rotation velocity in body frame from Gyroscope
- Infer the relative rotation of the IMU between two time instances

## Acceleration
- Must be “gravity-compensated” before use for state propagation
- Gravity分量的存在使得imu设备的绝对rotation（相对大地坐标系，但不包含yaw）变得可观
- Error Model
	- 确定性误差
		1. $l = sa + b$, $a$真值，$l$测量值
		2. Bias: $b$
			- 随时间变化，其变化可由维纳过程建模，离散情况下为random walk
			- 第二节IMU传感器->测量方程， eq 12
			- 艾伦方差标定
		3. Scale: $s$ → 真实值和测量值的斜率，通常为1
		4. 标定
			- 第二节IMU传感器->测量方程， eq 7
			- 面法: 标定s和b, 通过最小二乘法
	- 随机误差 → 均值为 0，方差为$\sigma$的白噪声
	- bias和高斯噪声的variance无法单纯通过自己的测量值获得，可以在vio的框架下通过visual cue来估计

## Angular velocity
- $w = [w_1, w_2, w_3]^T$
- 在imu的局部坐标系的测量值
- 可以理解为旋转矩阵$R$的李代数
	- $R' = Rw^\wedge$
	- $R = \exp(w^\wedge)$
- Zero Velocity Update
	- Idea: 当发现系统处于stationary的状态时，人工合成零速的imu测量值来更新系统
	- Ref: https://docs.openvins.com/update-zerovelocity.html
	- Approach:
		- Constant Velocity Synthetic Measurement
			- 设定线性加速度和角速度的真实值为零 → 不会直接强迫速度本身为零，因为imu不会直接获得速度
			- 获得Residual和Jacobian来更新系统
		- Zero Velocity Detection：Threshold-based → Velocity Resisual < Chi-squared error

# IMU Integration
- Kinematic Model → Forster eq 29
- Integration Model
	- 利用运动模型对每一个新增的测量值都进行积分，$\Delta t$
	- Forster eq 31:
		- 描述了在一段时间之后的$PVQ$关于一段时间之前的$PVQ$之间的关系
		- $PVQ(t+\Delta t) = F(PVQ(t), M(\Delta t))$
		- $M(\Delta t)$是imu测量值
		- 当把多个测量值合成到一起 →Forster eq 32
		- 在优化过程中，每一次$PVQ$的估计值的变化都会使eq 32需要重新算一遍，不经济

# IMU Pre-Integration
## Idea
- 获得$PVQ$在$\Delta t$中的变化量，跟$PVQ$的估计量解耦 → $\Delta PVQ = F(M(\Delta t))$
- 同时，残差函数的构造方式也不同
	1. 之前是直接对比某个时间的$PVQ$，计算残差
	2. 现在是使用两个时间段之间的$PVQ$的变换量的差别作为残差

## Preintegrated measurement model
- 考虑计算imu在$i$和$i+1$时刻之间的$\Delta R$, $\Delta V$和$\Delta P$
- 基于eq 32，在等式左右分别左乘一个$(R_i)^{-1}$，得到$\Delta R$，eq33；同时，$\Delta V$和$\Delta P$也可以通过$\Delta R$表示，获得eq33
	- 之所以是左乘是因为此时的$R$表示一个世界坐标系下的旋转量
	- $PVQ$的增量是相对于imu坐标的增量不是相对于世界坐标的增量
- 描述两个关键帧之间的状态变化的测量值和他们的噪声模型 Forster eq 38
- preintegrated mean
	- preintegrated rotation measurement: Forster eq 35
	- preintegrated velocity measurement: Forster eq 36
	- preintegrated position measurement: Forster eq 37
- preintegrated noise: Forster eq 39
	- linear function of the IMU measurement noise
	- zero-mean normally distributed
	- Forster eq 42-43
- Bias Update
	- 当Bias的增量比较小 → 利用$\Delta PVQ$增量关于Bias的线性化来更新$\Delta PVQ$, Forster eq 44，因为这时候线性化仍然在有效范围；
	- 当Bias变化量比较大 
		- 重新计算$\Delta PVQ$
		- 在vins-mono中在初始化求解solveGyroscopeBias()之后
- 积分过程可用的欧拉积分或中值积分
- Ref: On-Manifold Preintegration for Real-Time Visual-Inertial Odometry.pdf
		
## Residual
- 两个pose之间的imu预积分值减去两个pose的估计值变化量之差；
- 定义与pose graph里一致；