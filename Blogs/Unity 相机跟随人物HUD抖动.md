相机跟随人物移动而移动，但是人物的HUD抖动很严重。
人物HUD使用UGUI 2D制作是根据人物头顶的节点在世界坐标反算到UGUI坐标下的。

解决思路：
- 1，相机跟随当前pos和targetPos插值，Lerp后效果也不行。Lerp权重太小的话是可以解决抖动，但是跟随速度太慢，满足不了需求。
- 2，相机跟随代码放到LateUpdate中。依旧不行。
- 3，发现人物移动是通过对刚体设置velocity来驱动的，因此把相机跟随代码放到FixedUpdate中执行，且Lerp插值。同时把HUD坐标更新的代码也移到FixedUpdate中处理。完美解决。