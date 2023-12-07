---
share: true
---

# Problem

- 现在的地图质量评估基本上都是在有轨迹真值的情况下进行的；
- 计算地面厚度的办法只能评估z向的质量；
- 通过计算交通灯厚度来评估xy向质量的方法有问题，因为交通灯没有标准厚度；

# Approach

- Relative accuracy evaluation algorithm without gt；
- A method for detecting the degree of ghosting in point cloud map；
- 去除掉所有动态物体；

# Methode

- 使用$p_{acc}$描述bad poses的数量

## Input

- Pose序列；
- 每一帧观察到的点云；

## Process

1. 对每一帧，把相应的点云投影到世界坐标系；
2. 找到他附近的帧（半径范围），把他们的点云投影到世界坐标系形成一个submap；
3. 选取submap的某个点P，把他跟pose点O连起来，形成一条线OP，如果OP会和Submap上的点有交叉，则认为这是个鬼影点；
4. 评估造成鬼影点的pose的相对精度；

## Output

- 所有的bad pose