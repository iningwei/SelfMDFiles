目前2021.3.8f1中Unity的内置导航系统Navigation位于UnityEngine.AI命名空间下。


NavMeshAgent agent

- 立即停止寻路
使用agent.isStopped=true，可以停止寻路，但是有很大的延迟，视觉体验很不好。

使用agent.enabled=false，通过禁用NavMeshAgent组件的方式实现立即停止寻路。因为组件被禁用可能会产生其它问题，比如失去了Obstacle Avoidance的功能。

使用agent.ResetPath(),可以停止寻路，同isStopped一样，会有很大延迟。

使用agent.destination = agent.transform.position;虽然看起来很尴尬，但是可以满足需求。

- navAgent.SetDestination(point)
若目标点point不可达，这会为你分配一个当前Navmesh上一个可达的点。若无可达点，这返回false。

SetDestination缓慢的问题：
https://answers.unity.com/questions/431248/navmeshagent-setdestination-too-slow.html
NavMesh.pathfindingIterationsPerFrame = 50000;