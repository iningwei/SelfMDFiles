## job
### step1：概念理解，囫囵吞枣
深入理解JOBS：国人翻译：https://zhuanlan.zhihu.com/p/56459126

官方文档：https://docs.unity.cn/Documentation/Manual/JobSystemOverview.html
### step2：详细理解，并提供了更复杂的实例
官方文档：https://docs.unity3d.com/Manual/JobSystem.html
### step3：看简单demo
[job-system-cookbook](https://github.com/stella3d/job-system-cookbook)
[DOTS-training-samples](https://github.com/Unity-Technologies/DOTS-training-samples)

[unity-job-system-based-target-finding](https://answers.unity.com/questions/1899963/unity-job-system-based-target-finding.html)
## Burst
这里点击[Released packages](https://docs.unity3d.com/Manual/pack-safe.html) 找到com.unity.burst,点击后会看到介绍和各个版本的说明。
官方对burst的简介：
```
Burst is a compiler that you can use with Unity's job system to create code that enhances and improves your application's performance. 
It translates your code from IL/.NET bytecode to optimized native CPU code that uses the LLVM compiler.
```
简言之就是一种编译技术，可以大大提高job的效率。

## ECS
[Harnessing the power of ECS for Unity | Unity at GDC 2023](https://www.youtube.com/watch?v=WSrvUynsd34&list=PLX2vGYjWbI0TkxPwhWgsBhvj-EwxJDt5x&index=6)


[官方DOTS Guide and Samples](https://github.com/Unity-Technologies/EntityComponentSystemSamples)

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


5，[MinecraftECS](https://github.com/UnityTechnologies/MinecraftECS)

6，一个ECS帧同步框架[LittleBee](https://github.com/dudu502/LittleBee)


7，一个很好的ECS视频教程：[Unity ECS 1.0 Full Project Tutorial | Step-by-Step](https://www.youtube.com/watch?v=IO6_6Y_YUdE)
 ECS教程：https://forum.unity.com/threads/new-version-of-entities-1-0-pre-release-now-available-february-2023.1400887/ 
 
 ECS官方文档：https://docs.unity3d.com/Packages/com.unity.entities@1.0/manual/index.html
 
 

 8，
 [最新DOTS全面技术解析，从基本概念到应用原理](https://mp.weixin.qq.com/s/DtiA_790XTkuwKC9O-lJmw)
 
 [Unity中文课堂 | 学习DOTS不迷路，超全面宝藏教程带你上车](https://mp.weixin.qq.com/s?__biz=MzkyMTM5Mjg3NQ==&mid=2247541365&idx=1&sn=b8306c212645131751b7e6b96f7e6471&chksm=c18621f1f6f1a8e778172b2204197b359d98aaecb3c8a5d4cf83a83235e4d631228dc2d6b7cd&scene=21#wechat_redirect)



 9，
 [使用job system获得最近目标](https://answers.unity.com/questions/1899963/unity-job-system-based-target-finding.html)
 
 
 [Unity3DMMORPG技术分享: 基于ECS的技能释放与Buff管理详解](https://zhuanlan.zhihu.com/p/622784466)
 
 [大智_Unity大玩家 ECS专栏：第五页、第六页](https://space.bilibili.com/8052043/article)
 
 10，一个DOTS寻路开源工程：https://github.com/reeseschultz/ReeseUnityDemos