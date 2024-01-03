---
share: true
---

- Solve the nonlinear least square optimization

# Structure
- Vertex: parameter to be optimized
- Edge: edge between the nodes i and j represents an ordered constraint
- Error: 每一条边都代表一个measurement，error是measurement和以node为基础预测的measurement之差

# 非欧参数适配性
- SO2/SO3 Rotation Group

## Approach
- Error function is obtained through perturbation
- 旋转的增量
	- 旋转状态本身使用四元数
		- 过参数化的表示法
		- 为了避免奇异性
	- 旋转变化量使用angle-axis
		- 最小的参数化，不会给优化问题带来额外的限制
		- 由于本身很小，不会产生奇异性

# Sparsity in BA

1. Reordering into Pose and Landmark
2. Obtain the increment of pose using schur complement
	- 在舒尔补中只需要计算关于landmark的block diagonal matrix的逆矩阵, 计算量比直接求大的H的逆要小很多

# Usage

## 定义顶点和边的类型

- Vertex
	- CurveFittingVertex: public g2o::BaseVertex<3, Eigen::Vector3d>，继承自BaseVertex
	- 参数
		- 变量维度：3
		- 数据类型：Eigen::Vector3d
	- 方法
		- 重置：setToOriginImpl()
		- 更新：oplusImpl(const double* update)
- Edge
	- class CurveFittingEdge: public g2o::BaseUnaryEdge<1, double, CurveFittingVertex>，继承BaseEdge类
	- 参数
		- 观测值
			- 错误变量的维度：1
			- 数据类型：double
		- 连接的顶点类型：CurveFittingVertex
	- 方法
		- 误差计算：void computeError()

## 图构建

- 配置
	- 线性方程求解器
	- 误差项
	- 优化方法选择
- 构造
	- 添加顶点
		- 顶点初值：v->setEstimate(Eigen::Vector3d(0, 0, 0));
		- 顶点ID：v->setId(0);
		- 添加：optimizer.addVertex(v)
	- 添加边
		- 边ID：edge->setId(i);
		- 边的输入：
			- edge->setVertex(0, v);
			- edge->setMeasurement(y_data[i]);
		- 信息矩阵：dge->setInformation(Eigen::Matrix<double, 1, 1>::Identity()*1/(w_sigma*w_sigma));

## 优化
- jacobian：使用numeric difference计算

## Ref
- learn_g2o_fitting.cpp