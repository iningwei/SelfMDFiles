目前2021.3.8f1中Unity的内置导航系统Navigation位于UnityEngine.AI命名空间下。


NavMeshAgent agent

- 立即停止寻路
使用agent.isStopped=true，可以停止寻路，但是有很大的延迟，视觉体验很不好。

使用agent.enabled=false，通过禁用NavMeshAgent组件的方式实现立即停止寻路。因为组件被禁用可能会产生其它问题，比如失去了Obstacle Avoidance的功能。

使用agent.ResetPath(),可以停止寻路，同isStopped一样，会有很大延迟。

使用agent.destination = agent.transform.position;虽然看起来很尴尬，但是可以满足需求。

- navAgent.SetDestination(point)
若目标点point不可达，这会为你分配一个当前Navmesh上一个离目标point最近的可达点。若无可达点，这返回false。

- SetDestination缓慢的问题
若有大量的角色同时寻路，且伴随carve的重建，那么寻路会很耗时，表现出的结果就是角色很卡，可以通过设置``NavMesh.pathfindingIterationsPerFrame``为一个很大的值来规避。
SetDestination本身是一个异步方法，unity默认是限制了其每帧执行的次数的。通过上述设置提高了每帧执行的次数，但是本质上它还是一个单线程的方法。当角色海量时，还是会大大降低游戏帧率的。


- 判断可达
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