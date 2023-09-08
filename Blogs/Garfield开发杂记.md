- 贴图命名规则
主贴图：a_
金属度贴图：m_
法线贴图：n_
环境光遮蔽贴图：o_
自发光贴图：e_



## 20230803 星期四
- HairibarRagdoll开源工程深度理解
  - 工程自带工具，NAN问题的处理：（Animation Profile需要取消Match Root Rotation选项）
- 一些自动化Ragdoll工具实现
- 解决Ragdoll后，模型自身动作表现异常问题


## 20230814-20230818
- 框架调整使适合3个游戏的整合
- 玩法2 demo

## 20230823-20230824 星期三-星期四
- 玩法2 demo实现和完善
- TODO：对象回收增加个回收事件，用来处理组件的移除
- TODO：解决角色刚体挤压导致的穿透问题
（试着使用CharacterController，有两个问题，1，性能消耗更大；2，正向向前的时候表现效果比较好，被反推的时候穿透问题更严重）


## 20230825 星期五
- 解决了大量角色导致的穿透问题
- TODO：解决判断各个角色isPushingFinalTarget死循环的问题


## 20230828 星期一
- game02一些UI逻辑集成
- TODO：实现actor的onBeAttacked、onBeDamage、onDead事件 

## 20230829 星期二
- bug:角色死亡不消失
- 改双线对战NPC为鱼蛙模式，双方单线对战


## 20230830 星期三
- 行为树资源动态加载
- TODO：creatActor所在类调整
- TODO：处理贝塞尔追踪或者抛物线追踪子弹
- TODO：game02角色速度设置还有问题

## 20230831 星期四
- TODO：BulletAgent层级自己设置，不统一设置
- TODO：开局倒计时
- TODO：blog刚体对raycast最终目标的影响

## 20230901 星期五
- TODO：KDTree
- TODO：Bullet Physics插件


男人左手拿喷火器那个武器，其它角色都是右手
男人右手拿灭火器那个武器

## 20230902 星期六
``周末加班``
- 新引入几个角色


## 20230904 星期一
- 更换指令功能实现
- 家园警戒版本1.0.2-22更新
- TODO：测试Mesh Animator



## 20230905 星期二
- emoji功能70%
- game02三个水果子弹
- game02场景换进去
- 增加小加菲角色

## 20230906 星期三
- TODO：window要支持初始化传参数，而不是或者不仅仅是show的时候传
- 本局排行和周排行的UI拼接
- game02的UI拼接
- 集成SoftMask插件

## 20230907 星期四
- TODO：玩家角色复活逻辑
- game02战斗UI逻辑
- showTime倒计时逻辑
- TODO：同一个角色。玩家对应的actor和礼物对应的actor是不一样的ID，存在一些数值差异