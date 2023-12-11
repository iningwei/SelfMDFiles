

[万人同屏, 从入门到放弃之——Entities 1.0.16性能测试](https://blog.csdn.net/final5788/article/details/132953620)

[万人同屏, 从入门到放弃之——多线程RVO避障](https://blog.csdn.net/final5788/article/details/132891595)

[万人同屏, 从入门到放弃之——自定义BatchRendererGroup合批渲染海量物体](https://blog.csdn.net/final5788/article/details/132912995)

[十万人同屏寻路? 基于Dots技术的多线程RVO2避障](https://blog.csdn.net/final5788/article/details/133079504)

 
https://github.com/snape/RVO2-CS
 
作者博文中使用的BRG（BatchRendererGroup）代码为：[Creating draw commands](https://docs.unity3d.com/cn/2022.3/Manual/batch-renderer-group-creating-draw-commands.html)，unity2022版本才支持上面链接中的这些api；同时涉及到的BRG知识也可以这里获得！

集成RVO-DOTS和GPUInstancing后，（开启实时灯光和阴影），机器显卡2060，CPU i5-10400F。

使用龙之战的战士（顶点5780，三角面5191），同屏5K单位帧率为：41; 同屏1W单位帧率为21 
简化模型（顶点698，三交面507），同屏5K单位帧率为：46-47 ;同屏1W单位帧率为24

可见帧率几乎没有什么变化。说明在上述配置下压力都不在GPU。而是CPU。
 