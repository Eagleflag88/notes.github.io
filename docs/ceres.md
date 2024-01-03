---
share: true
---

- 通用最小二乘优化器

# 依赖项

## Eigen
- Dense QR
- Dense Cholesky

## SuiteSparse
- Sparse Cholesky

# Problem Modeling

## ResidualBlock

- CostFunction
	- r = 0.5*(c1^2 + c2^2 + ...)
		- c是cost function
		- 一般r是我们想要计算的目标函数
	- 计算residual和jacobian matrix
	- 方法
		- virtual evaluate()
		- virtual bool Evaluate(double const* const* parameters, double* residuals, double** jacobians) = 0;
		- paramters：2维array
		- residuals：1维array
		- jacobians：2维array
- SizedCostFunction
	- 当优化参数和残差的size确定时
	- 继承CostFunction：实现evaluate()
	- SizedCostFunction<1, 3>
		- residual dim = 1
		- 第一个参数组的dim = 3
		- 有可能有多个参数组
			- SizedCostFunction<1, 3, 3>
			- 所以Evaluate()函数里面的parameters是一个2维array
- AutoDiffCostFunction
	- 定义误差函数 as templated functor
		- 优化参数
		- 误差
		- 这样才能同时兼容jet的计算
	- CostFunction* cost_function = new AutoDiffCostFunction<MyScalarCostFunctor, 1, 2, 2>(new MyScalarCostFunctor(1.0));
		- MyScalarCostFunctor
			- functor
			- 自己定义
		- 1，2，2
			- 1 residual dim
			- 2，2，parameter dim
- rotation.h
- Ref： http://ceres-solver.org/nnls_modeling.html#costfunction
- 对每一个观察添加一个误差项
	- problem.AddResidualBlock，同时也会隐性的添加每个cost相关的parameterblock
		- problem.AddResidualBlock(new MyUnaryCostFunction(...), x1);
		- x1即是待优化变量，类型是double*
	- 模板参数：误差类型，输出维度，输入维度，Cost Function

## ParameterBlock

- 待优化变量
	- 定义为数组：double x1[] = { 1.0, 2.0, 3.0 };
	- 同时也初始化初值
- LocalParameterization
	- 系统实现的一个接口类
	- 定义被流形描述的变量
		- QuaternionParameterization
		- EigenQuaternionParameterization
	- 可以自己实现各种操作，比如加法和乘法
		- virtual bool Plus(const double* x,
                    const double* delta,
                    double* x_plus_delta) const = 0;
        - virtual bool ComputeJacobian(const double* x, double* jacobian) const = 0;
- 一般不会显性调用problem.AddParameterBlock

## Loss Function

- 处理outlier
- 关于cost的函数：rho(cost)
- 需实现方法：[rho, rho', rho'']

# Problem Solving

## Solver

- Trust Region
- ine Search

## LinearSolver

- DENSE_QR：Small Problem
- CHOLESKY：Large Problem
- Schur：BA Problem

## Preconditioner

- precondition the H matrix

## Ordering

- Idea
	- 解增量方程的时候的消元顺序
	- 每个paramterblock都绑定一个整数，表示这个parameterblock的顺序，最小的先消
	- {0:x},{1:y} → 先消x
- 实现
	- ParameterBlockOrdering：利用这个类设定ordering
	- ceres::ParameterBlockOrdering* ordering = new ceres::ParameterBlockOrdering;
	- ordering->AddElementToGroup(points + point_block_size * i, 0);
	- Ref： http://ceres-solver.org/nnls_solving.html#ordering
	- 十四讲 ceresBundle.cpp -→ SetOrdering()

## Best Practice

- 在使用schur补求解增量方程时应该把Group 0给路标变量，Group 1给位姿变量 → 因为需要先消除路标变量

# Automatic Differentiation

## Problem

- 已知一个函数f(x)
- 求f()在x=a处的导数

## Dual Number

- 定义
	- x = a+ b*epsilon → a和b为实数
	- epsilon^2 = 0
- Operation
	- 加减 → 实部虚部加减
	- 相乘
		- 先通过四则运算展开，让后利用性质整理
		- 虚部不会影响实部 → epsilon^2 = 0
- Approach
	- 计算f(a + b*epsilon)
		- b是任意选择的
		- 一般选1
	- 结果的就是f(a) + f'(a)*b*epsilon
	- epsilon可以通过矩阵来实现 → epsilon = [0 1; 0 0]
- Ref: https://blog.demofox.org/2014/12/30/dual-numbers-automatic-differentiation/