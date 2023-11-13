---
share: true
---
# Concept
基于[[2020-Kimera-Multi a System for Distributed Multi-Robot Metric-Semantic Simultaneous Localization and Mapping|2020-Kimera-Multi a System for Distributed Multi-Robot Metric-Semantic Simultaneous Localization and Mapping]]

# Improvements
使用新的后端优化：
- Stage 1：使用一个robust的算法初始化各个机器人之间的关系；
- Stage 2：使用graduated non-convex算法来优化pose，并且剔除回环外点，GNC的方法比PCM更加稳定，因为PCM经常会过于保守，降低召回率，而且性能跟参数相关性高

# Code

https://github.com/MIT-SPARK/Kimera-Multi
https://github.com/MIT-SPARK/Kimera-Multi-Dat