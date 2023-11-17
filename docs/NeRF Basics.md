---
share: true
---
# Rendering
- Process of generating an image from a 2D or 3D model using a computer program；
- Also known as Image Formation；
	
## Concept
- Light：使用photon的概念描述，一般包含他的能量，波长，方向和位置等等；
- Radiance：光子的density field
	1. 通过plenoptic function描述：represents the intensity of the light observed from every position and direction in 3-dimensional space → 是一个5维函数；
	2. Incoming Radiance：field radiance → 某个交界处看向外界环境感受到的radiance；
	3. Outgoing Radiance：surface radiance → 空中某个点观察surface获得的radiance；
- Ref：
	1. Basics of physically-based rendering.pdf
	2. https://www.pbr-book.org/3ed-2018/contents

## Process
- 基于渲染方程计算出一个对象向一个观察者的radiance；
- Input：所有关于场景的信息 → scene file；
- Output：渲染出的图片；
- 分类：
	1. Rasterization：把空间中的物体通过几何元素进行栅格化（一般是mesh），然后把这些栅格直接投影到2D平面，很难考虑optical effects；
	2. Ray Casting：Ray Casting 是一种通过模拟光线从视点发射并与场景中的对象相交来生成图像的技术。考虑从某个视角出发射出目光到空间物体的过程，在射线上进行采样，采样点的性质（色彩，透明度...）累积起来决定了观察到的scene。考虑了几何关系和最基本的光学效果；
	3. Ray Tracing：跟Ray Casting类似，但是考虑了更加复杂的光学效果，比如说折射和反射；

# Surface vs. Volume Rendering

Surface Rendering 和 Volume Rendering 是计算机图形学中用于生成 3D 图像的两种不同渲染技术。它们在处理 3D 数据和生成最终图像的方式上有本质的区别。

1. Surface Rendering:
    - 定义：Surface Rendering 关注于渲染 3D 模型的表面。它通常涉及将 3D 模型（如多边形网格）转换成 2D 图像的过程；
    - 技术：这种技术使用光栅化（Rasterization）或光线追踪（Ray Tracing）来计算表面上的光照、阴影、纹理和反射效果；
2. Volume Rendering:
    
    - 定义：Volume Rendering 是一种直接从 3D 数据集生成图像的技术，不需要将数据转换为中间的表面表示。它通常用于渲染不透明度和颜色随空间变化的数据；
    - 技术：这种技术涉及到对整个数据体积内的每个点进行采样和计算，以模拟光线穿过具有不同密度和材质的媒介；

**联系与区别**:

- 联系：两者都是用于从 3D 数据生成 2D 图像的技术。它们可以使用类似的光照和着色技术来增强渲染效果；  
- 区别：  
    - 数据类型：Surface Rendering 主要处理清晰定义的表面（如多边形网格），而 Volume Rendering 直接处理 3D 数据体积；
    - 渲染过程：Surface Rendering 侧重于表面的细节，如光照和纹理。Volume Rendering 需要计算整个数据体积，处理更为复杂，因为它需要考虑数据中每个点的贡献；
- Ref：Advances in Neural Rendering.pdf