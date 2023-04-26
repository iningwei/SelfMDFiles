目前2021.3.8f1中Unity的内置导航系统Navigation位于UnityEngine.AI命名空间下。
 
## 立即停止寻路

使用agent.isStopped=true，可以停止寻路，但是有很大的延迟，视觉体验很不好。

使用agent.enabled=false，通过禁用NavMeshAgent组件的方式实现立即停止寻路。因为组件被禁用可能会产生其它问题，比如失去了Obstacle Avoidance的功能。

使用agent.ResetPath(),可以停止寻路，同isStopped一样，会有很大延迟。

使用agent.destination = agent.transform.position;虽然看起来很尴尬，但是可以满足需求。

使用carve,即需要为物体添加NavMeshObstacle组件，在需要停止的时候启用该组件，并设置Carve为true，同时关闭NavMeshAgent（NavMeshObstacle和NavMeshAgent两个组件无法共存）

## navAgent.SetDestination(point)

若目标点point不可达，这会为你分配一个当前Navmesh上一个离目标point最近的可达点。若无可达点，这返回false。

## SetDestination缓慢的问题

若有大量的角色同时寻路，且伴随carve的重建，那么寻路会很耗时，表现出的结果就是角色很卡，可以通过设置``NavMesh.pathfindingIterationsPerFrame``为一个很大的值来规避。
SetDestination本身是一个异步方法，unity默认是限制了其每帧执行的次数的。通过上述设置提高了每帧执行的次数，但是本质上它还是一个单线程的方法。当角色海量时，还是会大大降低游戏帧率的。


## 判断可达
```csharp
 NavMeshPath path = new NavMeshPath();
                    agent.CalculatePath(targetPos, path);
                    if (path.status == NavMeshPathStatus.PathComplete)
                    {
                        //reachable
                        agent.SetPath(path);
                        //agent.SetDestination(targetPos);//already CalculatePath,so use SetPath is more effective than SetDestination
                    }
                    else
                    {
                        //not reachable
                    }
```

## 设置多个寻路层
- 比如需求是寻路层有：普通道路、桥。
- 自带的默认层有Walkable、Not Walkable、Jump。在Navigation->Areas下增加新层Bridge。注意这个界面中每层的颜色最终也反应在烘焙网格的Gizmos显示中。流程如下：
  - 1，为桥所在的物体添加NavMeshModifier，设置其Area Type属性为Bridge。桥所在的物体选中后，打开Navigation->Object，设置其Navigation Area为Bridge。其它行走区域设置Navigation Area为Walkable，障碍区域设置Navigation Area为Not Walkable
  - 2，NavMeshSurface烘焙行走区域
  - 3，为NavmeshAgent设置可行走区域,如：``agent.areaMask = 1 << 0 | 1 << 3;``
- 从指定点找到导航网格中的最近点

NavMesh.SamplePosition(Vector3 sourcePosition, out AI.NavMeshHit hit, float maxDistance, int areaMask)，``bool True if the nearest point is found.``参考:[NavMesh.SamplePosition](https://docs.unity3d.com/ScriptReference/AI.NavMesh.SamplePosition.html)

## NavMeshAgent寻路单位重叠(``大量带有NavMeshAgent的单位寻路到相同点时，在寻路过程中会重叠，到达目标点后也会重叠``)
### 方法一，使用Obstacle Avoidance。
文档中说Obstacle Avoidance使用的rvo，但是效果不是太好，实测不同物体之间碰撞后产生的推挤、抖动很严重。
如果你想各个单位能够更好的到达终点则每个单位的终点并不应该设置为同一个点，你可能需要根据每个角色的寻路半径来预先随机设置每个角色会到达的对应的位置，这样在寻路时才不会互相推挤。
### 方法二，使用NavMeshObstacle来挖洞。
具体流程是给物体同时挂上NavMeshAgent（不开启Obstacle Avoidance）和NavMeshObstacle组件（注意两个组件不可同时开启否则会产生不可控的瞬移）。在移动时关闭NavMeshObstacle组件，启用NavMeshAgent进行寻路；在到达目标点后先关闭NavMeshAgent，再启用NavMeshObstacle组件来挖洞，这样会对Navmesh路网进行重建，其它物体寻路路线也会变化最终会绕着目标形成包围圈。

根据上述内容知道，物体挖洞后再恢复寻路则需要禁用NavMeshObstacle，并启用NavMeshAgent。相关伪代码如下：
```csharp
navObstacle.enabled=false;
navObstacle.carving=false;
navAgent.enabled=true;
```
然而事实是这样的话也会使物体发生不可控的瞬移，原因就是NavMeshObstacle的禁用不是立即完成的，上述代码会导致在当前帧NavMeshObstacle、NavMeshAgent都处于开启状态。因此在禁用NavMeshObstacle后必须至少延迟1帧才能开启NavMeshAgent。

参考：https://github.com/llamacademy/ai-series-part-33


## 其它
git上一个基于dots的nav寻路：https://github.com/dotsnav/dotsnav