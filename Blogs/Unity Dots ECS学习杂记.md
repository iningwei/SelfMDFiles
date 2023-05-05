## job
### step1：概念理解，囫囵吞枣
深入理解JOBS：国人翻译：https://zhuanlan.zhihu.com/p/56459126 ,  官方文档：https://docs.unity.cn/Documentation/Manual/JobSystemOverview.html
### step2：详细理解，并提供了更复杂的实例
官方文档：https://docs.unity3d.com/Manual/JobSystem.html
### step3：看简单demo
[job-system-cookbook](https://github.com/stella3d/job-system-cookbook)
[DOTS-training-samples](https://github.com/Unity-Technologies/DOTS-training-samples)
## Burst
这里点击[Released packages](https://docs.unity3d.com/Manual/pack-safe.html) 找到com.unity.burst,点击后会看到介绍和各个版本的说明。
官方对burst的简介：
```
Burst is a compiler that you can use with Unity's job system to create code that enhances and improves your application's performance. 
It translates your code from IL/.NET bytecode to optimized native CPU code that uses the LLVM compiler.
```
简言之就是一种编译技术，可以大大提高job的效率。

## ECS




## 其它
1，UniteAustinTechnicalPresentation Nordeus demo和安装:https://blog.csdn.net/vic_torsun/article/details/115011482
2，基于dots实现的pathfinding:https://zhuanlan.zhihu.com/p/397110654
dots有一系列文章：https://www.zhihu.com/column/c_1304460571526750208
作者有很多干货：https://www.zhihu.com/people/yakshaver

3，dots官方教程：https://learn.unity.com/course/dots-best-practices 

Pathfinding in Unity DOTS! (Insane Speed!!!)：
https://www.youtube.com/watch?v=1bO1FdEThnU&list=PLzDRvYVwl53s40yP5RQXitbT--IRcHqba&index=7


Pathfinding in Unity ECS! (Epic Performance!)：
https://www.youtube.com/watch?v=ubUPVu_DeVk&list=PLzDRvYVwl53s40yP5RQXitbT--IRcHqba&index=7



Entities官方文档：https://docs.unity3d.com/Packages/com.unity.entities@0.51/manual/index.html
Hybrid Renderer官方文档：https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@0.51/manual/index.html
目前不支持默认BuildIn渲染管线，因此在默认管线里面创建的entity物体会出现无法显示的问题(the built in render pipeline isn't supported by Hybrid Renderer V2)。



Entities 0.17升级到0.5的注意事项
https://docs.unity.cn/Packages/com.unity.entities@0.50/manual/upgrade-guide.html#systembase


4，ECS github 官方demo：https://github.com/Unity-Technologies/EntityComponentSystemSamples/

5，[MinecraftECS](https://github.com/UnityTechnologies/MinecraftECS)