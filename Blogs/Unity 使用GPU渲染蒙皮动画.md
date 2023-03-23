蒙皮动画需要使用CPU进行计算，目前官方还没有出DOTS版本的蒙皮动画方案，因此当同屏蒙皮动画数量过多时会大量降低帧率。

当前可行的解决方案是使用GPU来进行蒙皮动画的计算，核心实现就是把蒙皮动画关键帧数据记录下来，通过在Shader的vert阶段使用GPU进行动画表现（即顶点动画）。

当前在github和assetstore上已经有很多相关方案，笔者不再赘述详细的流程，这里摘取一些笔者认为还不错的方案链接，以供参考：
## github开源方案
1，Unity官方方案 https://github.com/Unity-Technologies/Animation-Instancing

https://www.jianshu.com/p/bb13de4127ca
https://blog.csdn.net/huutu/article/details/109759751
作者自己的分享：[基于Animation Instancing的人群模拟](https://zhuanlan.zhihu.com/p/28255631?ADUIN=252347463&utm_id=0)


https://zhuanlan.zhihu.com/p/36896547


2，
https://github.com/chengkehan/GPUSkinning

## assetstore方案
1，[GPU Instancer 75$](https://assetstore.unity.com/packages/tools/utilities/gpu-instancer-117566)

演示应用中飞船的操纵方式值得参考，有意思~
2，[GPU Instancer - Crowd Animations 60$](https://assetstore.unity.com/packages/tools/animation/gpu-instancer-crowd-animations-145114)

和1是同一家公司的系列产品。1、2由于价格昂贵，笔者并未实测，从演示应用看是能满足需求的

## 其它
https://blog.csdn.net/huutu/article/details/109759751