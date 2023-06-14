Unity寻路目前用的较多的是自带的navmesh或者A\*方案，一般来说我们把要寻路的对象称之为智能体。寻路中的障碍可以分成静态障碍和动态障碍。
静态障碍诸如房子、静止的车子、路灯等一些不动的障碍物。动态障碍就复杂了，比如行驶的车子、其它行走的智能体。
## 静态障碍处理
若场景中只存在静态障碍的话，navmesh和A\*都比较好处理。navmesh是预先就烘焙好了可行走区域的，再根据目标点和起始点在可行走网络中生成路径：[NAV导航网格寻路（2） -- 寻路方法](https://www.cnblogs.com/zdlbbg/p/4301266.html)；A\*是根据先验地图已有的障碍物和Free区域信息规划一条起始点到目标点的路径。
## 动态障碍
### navmesh
可以通过为障碍添加NavMeshObstacle组件来动态更新可行走区域。但是对于已经添加了NavMeshAgent组件的其它智能体，是不能添加NavMeshObstacle组件的（组件冲突）。官方推荐做法是智能体之间可以通过设置NavMeshAgent组件的AvoidancePriority的优先级来控制其躲避，笔者测试后效果并不佳，特别是场景内智能体较多，会遇到两个智能体面对面卡着互不相让的情况。
### A\*
上述提到A\*由于是预先就会根据起点、终点生成路径结果，因此默认情况下智能体根据路径结果移动的过程中是不能动态避障的。
对于Unity，可以在智能体移动的过程中实时根据智能体当前位置和终点计算新的寻路结果，从而达到动态避障的效果。
这种方法在场景网格划分不是太密级，智能体也不算太多的情况下效果还是不错的。若网格量级比较大或者寻路智能体较多的话会比较耗时，可以考虑不逐帧率，而是间隔一定的短时间来更新智能体新的路径。
基于原始A\*的动态障碍算法除了下面提到的D\*，还有其它一些实现方式，具体可以查看相关论文。
A\*算法简介：[如何在地图上开拓一条“星”路？A星寻路算法介绍及实现](https://zhuanlan.zhihu.com/p/51484976)，[寻路算法——Jump Point Search](https://rhetty.github.io/2018/02/25/%E5%AF%BB%E8%B7%AF%E7%AE%97%E6%B3%95%E2%80%94%E2%80%94Jump-Point-Search/),[RTS游戏中的寻路](https://zhuanlan.zhihu.com/p/109108400)
### D\*
D\*算法是一种以A\*为基础可以实时根据动态障碍物的位置修改已生成路径的算法。Stentz（卡内及梅隆机器人中心）在1994和1995年两篇文章提出，主要用于机器人探路。最早用于火星探测器采用的寻路算法。 D是一种启发式的路径搜索算法，适合面对周围环境未知或者周围环境存在动态变化的场景。
``D\*算法又称为动态A\*算法，在未知环境或有动态障碍物出现时，采用A\*算法需要丢弃初始规划完成的open表和close表，重新进行规划。造成规划时间的增加，D\*算法的核心思想是先用dijkstra或A\*从目标点向初始点进行反向搜索，然后机器人从起点向目标点移动，当遇到动态障碍物时，只进行局部的更改即可，效率明显提高。``

- D\*算法流程参考：[D*算法](https://blog.csdn.net/weixin_48648760/article/details/113762668)
- 一个D\*的C#版本实现：[DStarLite](https://github.com/Bastiantheone/DStarLite)

## 避障算法
对避障效果表现要求更高的情况下（比如RTS游戏、人流模拟等）就需要引入避障算法，避障算法也一路从VO（Velocity Obstacles）进化到了RVO（Reciprocal Velocity Obstacles）和ORCA(Optimal Reciprocal Collision Avoidance)
这篇文章有对避障算法进行了详细深入的介绍：[避障算法-VO、RVO以及ORCA(RVO2)](https://indienova.com/u/511421412/blogread/30380)
避免上面链接失效，这里还有一个：[避障算法-VO、RVO 以及 ORCA（RVO2）](https://new.qq.com/omn/20220315/20220315A01GKM00.html)
### RVO
算法原理、演示地址：http://gamma.cs.unc.edu/RVO/
Unity插件[A* Pathfinding Project Pro](https://assetstore.unity.com/packages/tools/ai/a-pathfinding-project-pro-87744?aid=1011l7JId&utm_campaign=unity_affiliate&utm_medium=affiliate&utm_source=partnerize-linkmaker) 有对RVO的实现
国内一个介绍RVO的文章：https://www.jianshu.com/p/8fc4e90e3850

### ORCA(RVO2)
ORCA实际上是在RVO做了一步升级，也可称之为RVO2。算法原理演示地址：https://gamma.cs.unc.edu/ORCA/
RVO2的Unity实现:https://github.com/warmtrue/RVO2-Unity
ORCA的Unity实现:https://github.com/wangjiong/ORCA
ORCA介绍比较好的文章：[RTS游戏中的避障算法](https://zhuanlan.zhihu.com/p/109556568)
[RVO和ORCA它们是如何工作的？](https://www.bilibili.com/read/cv7242625)


## 补充
- 一个使用unity burst compiled jobs 实现的A\*寻路系统（依旧使用Navmesh烘焙，然后提取数据）:[UnityAStarNavigation](https://github.com/jeffvella/UnityAStarNavigation)
- 基于A\*PathfindingProject扩展的dots版本：[dots-pathfinding-orca](https://github.com/SpikyAndrew/dots-pathfinding-orca)
- [导航动态避让算法RVO的优化ORCA(Optimal Reciprocal Collision Avoidance)](https://blog.csdn.net/u012740992/article/details/89397714#comments_13246451)