## 什么是GPU Bound
这是一个经常会碰到的常见问题，现象主要表现为隔一会卡顿一下，如果你用Profiler看一般就是看到间隔性出现大波峰。随便找了张如下图:
![Unity Profiler下的GPU Bound](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210428190229.png)

若游戏没有使用多线程渲染，出现GPU Bound的时候游戏整体流畅度会因为渲染瓶颈严重下降，间隔性卡顿严重影响游戏体验。

## Draw和Present
通常更新会由Draw和Present两个渲染操作构成。Draw操作的时候会向API的指令缓冲发送渲染请求。并不阻塞游戏。Draw操作同时包含了CPU的Draw运算和GPU的渲染预算。而Present的时候则会处理一系列刷新操作从CPU转向GPU处理, 直接Block当前的游戏主线程。

通常GPU Bound是由执行Present的时候的阻塞, CPU在等待GPU处理无事可做导致的。如下图所示:
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210428190752.png)
上图是一个好的情况下的影响游戏帧时间的GPU Bound, 通常Frame2里这样的CPU Idle会导致游戏帧数下降。
但是有一个最坏的情况, 就是由于填充率(Fillrate)过大导致的GPU Bound甚至会出现跳帧现象。
通常这都是由在视野同屏内出现大填充率的物体导致。比如带多个光照处理shader并进行大量树叶遮挡Alpha Test的树。
(在SGX这样Alpha Blend性能高于Alpha Test的硬件上则更应该避开大量的AlphaTest的使用, 极端一点虚幻的ios版演示里直接将树叶的shader使用Alpha Blend而不用Alpha Test)
大致CPU和GPU交互情况如下图:
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210428190945.png)
上面这种跳帧情况甚至可能会直接出现在你的游戏帧数很高的时候。明明帧数很高, 但是却依然由于负载跳帧。


## 优化方法
- 保证游戏更新时间的稳定性

更新的Delta时间影响的是游戏里使用Delta时间驱动的其他游戏时间相关的更新子系统, 游戏加帧平滑可以避免帧数大幅度跳动。大致方法是采用帧时间采样历史队列取多帧平均时间, 避免DeltaTime的大幅度跳动。不过实际上帧平衡只能优化更新时间平滑和时间相关的子系统性能, 不能彻底解决GPU Bound导致的CPU Idle问题。

- 充分利用CPU Idle的时间给其他系统更新

Ogre采用的方法, 把游戏的更新插入在Draw操作后Present前, 这样会导致游戏更新比渲染慢一帧, 但是对实际游戏基本没有影响。如Game Engine Architecture说的, 现代游戏引擎的更新都会比渲染慢一帧的问题说的就是这个处理。

- 进行限定游戏帧率的负载平衡
通过Sleep将跳帧的渲染负载平均给多帧处理, 对游戏的每帧的渲染时间进行采样, 然后取历史平均值, 将低于最高跳帧的渲染时间其他帧的时间Sleep给最高值, 避免在GPU吃不消的时候还频繁发送指令。
和垂直同步一样会影响游戏帧率。(最高的渲染时间被平均负载了)

- 激活垂直同步
同步CPU和GPU到显示器的刷新频率。可以减少撕裂和Bound导致的一些现象。但是相对的也比较吃性能，一般选择给玩家开关。

- 多线程渲染
抽象渲染操作, 分离游戏的更新线程和渲染线程, 采用基于生产者消费者的渲染缓冲处理。大致有Ring Buffer(Unreal)和Double Buffer(CryEngine)两种实现方式。需要游戏引擎有良好的渲染系统抽象和构架。开发, 设计和重构成本通常较大。

当然以上只是技术上的缓解方案，根源问题还是得从导致性能问题的内容或者代码上入手。


## 其他Tips

- 似乎在A卡和N卡Draw和Present的处理机制不大一样。
- 这2年Intel的多核CPU+集成显卡构成的笔记本在市场上普及率很高。所以这类硬件需要考虑, Intel的集成卡有GPU Bound时自动超频的特性。
- 通常填充率会成为低端机的最大的瓶颈, 不好好处理好树和树叶这样高填充率的预算和Shader, 将会成为性能瓶颈的根源。
- 在PC平台大量的Alpha Test经常都是导致GPU Bound瓶颈的根源。


## 补充
文章来源[关于游戏性能预算，剖析，优化以及GPU Bound问题的一点心得](https://zhuanlan.zhihu.com/p/22112254)，普适于游戏引擎，这里放在unity栏目中，供参考