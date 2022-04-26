运行时生成Navmesh官方已经给了方案：[NavMeshComponents](https://github.com/Unity-Technologies/NavMeshComponents) ，同时官方还有配套教程:[Runtime NavMesh Generation](https://learn.unity.com/tutorial/runtime-navmesh-generation?tab=overview#)


## 需要注意的点
智能体需要通过代码动态添加NavMeshAgent组件，然后调用NavMeshSurface组件的BuildNavMesh()方法。

寻路过程中停止寻路：
agent.ResetPath();//该API可以清除当前路径，从而停止智能体。
通过设置agent.isStopped=true是无法停止智能体的


## 其它
[Runtime Generation of Unity NavMesh on the XY plane with 2D Physics](https://www.spacebumfuzzle.com/runtime-generation-of-unity-navmesh-on-the-xy-plane-with-2d-physics/)