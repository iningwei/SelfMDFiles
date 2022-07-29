对于一个游戏来说，有两种主要的计算资源：CPU和GPU。它们会互相合作，来让我们的游戏可以在预期的帧率和分辨率下工作。CPU负责其中的帧率，GPU主要负责分辨率相关的一些东西。



## CPU性能瓶颈

CPU性能瓶颈往往由以下因素造成：

- Draw Calls 
- 复杂的脚本
- 物理模拟（布料、碰撞、粒子）



## CPU优化

优化的方向根据类型可以包括：

### Draw Calls

- 批处理

  静态批处理：设置物体为静态，运行后会自动合并网格，会造成占用更多的内存(https://docs.unity3d.com/Manual/DrawCallBatching.html)

  动态批处理：限制因素很多，包括顶点属性不能超过900，缩放尺度的要求；使用了lightmap、多Pass Shader、实时阴影的都无法批处理。

- 图集

  

  

## 参考文档
[【Unity技巧】Unity中的优化技术](https://blog.csdn.net/candycat1992/article/details/42127811)