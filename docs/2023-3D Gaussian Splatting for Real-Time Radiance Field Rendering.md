---
share: true
---
# Problem
- 现在的Radiance Field技术没办法实时的渲染高解析度（1080p）的场景；
- NeRFs are a continuous representation implicitly representing empty/occupied space。浪费计算资源；

# Approach
- We represent the scene with 3D Gaussians that preserve desirable properties of continuous volumetric radiance fields for scene optimization while avoiding unnecessary computation in empty space；

## Scene Representation
整个场景由点云组成，每个点可以理解为一个3D Gaussian，defined by：
	1. A 3D position (mean)
	2. A 3D covariance matrix，3x3的矩阵，这是各向异性的，能够比较好的还原真实场景；
	3. A opacity $\alpha$
	4. Spherical harmonics coefficient → Color

## Optimization without Constraint

- 整个算法的目标是不断调整每个点的所有参数（上面的4个定义），来最小化某个Loss；
- 那么上面的3D Covariance Matrix不适合参与优化，因为它还附带一个条件，就是它必须是半正定的，这个条件不太好放到优化问题中，所以我们不用3x3的矩阵的矩阵表示，而是用一个Scaling Matrix S 和一个四元数（表示旋转）的结合：
$$
\sum=RSS^{T}R^{T}
$$
- 这样我们就可以直接进行无限制的优化了；

# Training

## Initialization
- Mean from the point cloud；
-  Initial covariance matrix as an isotropic Gaussian with axes equal to the mean of the distance to the closest three points.
## Rendering
- 把3D Gaussian投影的各个图片上；
- Point-based α-blending；

## Loss

## Adaptive control of Gaussians

- control the number of Gaussians and their density over unit volume；
- 每隔100迭代进行一次稠密化，地点就集中在构建不好的区域，通过 view-space positional gradients这个指标来量化；具体方法如下
	1. Clone：点较少的区域；
	2. Splitt：大的Gaussian；
- 踢掉透明的Gaussian，通过$\alpha$；

## Differentiable Raterization

1. Splitting the screen into 16×16 tiles
2. Cull 3D Gaussians against the view frustum and each tile
3. Sorting of Gaussian  by depth；
4. Rasterization，For each pixel, iterate over each gaussian front-to-back, blending them together → Image

# Process

1. A set of images of a static scene；
2. Cameras calibrated by SfM → 最终，我们得到点云和camera poses；
3. Rasterize the gaussians to an image using differentiable gaussian rasterization (more on that later)；
4. Calculate the loss based on the difference between the rasterized image and ground truth image；
5. Adjust the gaussian parameters according to the loss；
6. Apply automated densification and pruning；

# Ref
[Hugging Face Blog](https://huggingface.co/blog/gaussian-splatting)




