Mono的好处：
1）久经考验；
2）出包速度快；
3）包体略小；
4）支持安卓上DLL动态载入程序集热更新（本人没有用过，不肯定IL2CPP完全不支持）。

IL2CPP的好处：
1）没有堆内存只涨不降的问题；
2）C#侧的计算密集型算法性能暴增N倍（我们项目的同地图寻路、自动建筑位置规划等算法的速度都有3倍以上提升）；
3）Lua热更无任何问题，反射调用还是导出式调用都很完美支持（应该不能算优点，但确实在我的预料之外）。

再说一下IL2CPP的问题：

构建时间长好多，安卓NDK的windows版工具链怎么就能性能这么低呢！AMD 16核32线程撕裂者走起，NDK编译时完美支持并行化的，核多了编译慢也不是事儿了；
IL2CPP后，如果仅编译ARMv7的版本，部分模拟器无法启动游戏。用FAT（ARMv7 + x86）就可以了，而且模拟器上运行也非常流畅了，当然包又大了20多MB（我们的c#代码就是这么多…）。
Mono模式已经不可能支持64位了，面向未来，IL2CPP是唯一的选项，Unity现在已经做得很好了。

