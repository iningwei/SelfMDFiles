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
- （已处理）TODO：对象回收增加个回收事件，用来处理组件的移除
- （已处理）TODO：解决角色刚体挤压导致的穿透问题
（试着使用CharacterController，有两个问题，1，性能消耗更大；2，正向向前的时候表现效果比较好，被反推的时候穿透问题更严重）


## 20230825 星期五
- 解决了大量角色导致的穿透问题（刚体a设置速度对刚体b碰撞后，其实会对刚体b施加力的，过多累积的施加最终会导致穿透）
- （已处理）TODO：解决判断各个角色isPushingFinalTarget死循环的问题


## 20230828 星期一
- game02一些UI逻辑集成
- （已处理）TODO：实现actor的onBeAttacked、onBeDamage、onDead事件 

## 20230829 星期二
- （已处理）bug:角色死亡不消失
- 改双线对战NPC为鱼蛙模式，双方单线对战


## 20230830 星期三
- 行为树资源动态加载
- TODO：creatActor所在类调整
- TODO：处理贝塞尔追踪或者抛物线追踪子弹
- （已处理）TODO：game02角色速度设置还有问题

## 20230831 星期四
- （已处理）TODO：BulletAgent层级自己设置，不统一设置
- （已处理）TODO：开局倒计时
- TODO：blog刚体对raycast最终目标的影响
（射中子collider，若其父物体有rigidbody，最终hit返回的对象是父物体）
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
- （已处理）TODO：玩家角色复活逻辑
- game02战斗UI逻辑
- showTime倒计时逻辑
- （已处理，actor表新加角色）TODO：同一个角色。玩家对应的actor和礼物对应的actor是不一样的ID，存在一些数值差异

## 20230908 星期五
- （已处理）TODO：礼物特效播放，美术那边已经做好了。
- UI一些代码重构，礼物展示UI整理

## 20230909 星期六
``周六加班``
- 一些框架重构


## 20230911 星期一
- 集成礼物特效播放逻辑
- 弹幕入场逻辑
- game02角色死亡重新加入
- （已处理）TODO：角色血条
- TODO：game02礼物加角色推力逻辑
- TODO：登录流程，并和服务器测试结算，先跑通game02和总榜
- TODO：关卡串起来，换玩家阵营等逻辑

## 20230912 星期二
- 登录、结算流程跑通，还差结算部分逻辑未处理
- TODO：和文哥确认下。落座协议需不需要调整
- （已处理）TODO：排行前10进场特效
- TODO：排行榜11-100进场特效
- （已处理）TODO：GameBattleWindow 增加属性，使不被自动注册窗体类型

## 20230913 星期三
- TODO：RealtimeRankShowArea 界面还没有处理完
- TODO：game02礼物炸弹和礼物网功能
- 窗体名和窗体注册功能优化，增加ignore标签
- 关卡总榜功能调通


## 20230914 星期四
- game02的南瓜炸弹和捕狗功能
- （已处理TODO：11-100入场效果，动作有问题，需要涛哥处理

## 20230915 星期五
- （已处理）TODO：捕狗网方向的问题，通过在bullet表里加个字段控制子弹转向
- 游戏结束提示界面
- TODO：捕狗车网兜住角色，角色直接消失，不应当再表演死亡动作


## 20230918 星期一
- 帮助长远处理game03 hud 抖动问题
- （已处理）TODO：转场动画和转场逻辑

## 20230919 星期二
- （已处理）TODO：需要有一个专门记录玩家的脚本，用来在新关卡的时候创建Player和Actor
- （已处理）TODO：有个记录玩家各个关卡得分信息的脚本，用来总榜上传数据


## 20230920 星期三
- （已处理50%）TODO：捕狗猫车子改成无视碰撞，且遇到PushTarget不再前行
- （已处理）1-10大哥入场特效加玩家信息，采用UI overlay的方式
- （已处理）人数上限问题
- （已处理）TODO：关卡2、3关卡进入后自动生成之前加入的玩家
- （已处理）TODO：game02部分礼物需求变动，需要等美术资源调整到位后，我这边跟着调整
- （已处理）TODO：game02到game03后，创建角色会报错

## 20230921 星期四
- 完善送礼提示逻辑
- 继承角色在新关卡自动加入游戏
- （已处理）TODO：LoadOtherPrefab函数中reUse去除
- （已处理）TODO：表情功能丢失？


## 20230922 星期五
- （已处理）TODO：登录界面
- 彻底解决game03,HUD抖动问题(刷新帧率不同导致的)
- （已处理）TODO：
为了出包hacker处理了两个问题
1，取消了热更（需要恢复宏，以及加上热更dll）
2，打包后shader不生效，启用了ResetEditorShader。
3，后面得查查为啥之前的比如家园警戒的shader是没有问题的

## 20230925 星期一
- TODO：重写捕狗车行为树，改成先直接开过去，射线检测，碰到栅栏就停下找目标扔网
- 关卡过渡机制优化，使得视觉效果更自然
- （已处理）TODO：emoji表情增加指示箭头
- game02相机镜头优化

## 20230926 星期二
- game02捕狗车表现优化
- TODO：结算界面处理
- TODO：[Unity Entities 使用策略：从原理到最佳实践](https://mp.weixin.qq.com/s/2GhM8kqwEbPea5Yy1HD7Vg)

## 20230927 星期三
- TODO：通用对象池
- （已处理）TODO：机器人继承

## 20230928 星期四
- 关卡加载入场UI效果
- 一些UI优化



## 20231007 星期六
``中秋国庆8天长假后调休``
- 贝塞尔子弹增加实时追踪功能
- （已处理）TODO：引入kdtree大幅提高性能；500单位可以维持在28帧左右。1000单位降到10帧
- TODO：KDTree深入学习：https://zhuanlan.zhihu.com/p/529487972
- 全局hud不可见情况下，角色重生瞬间UI闪烁的问题
- （已处理）TODO:Mesh Animator丢失骨骼信息，game01需要依赖骨骼绑定一些节点，因此game01不使用该方案
- Game02集成了MeshAnimator，有一些问题
   - （已处理，是有同名动作导致的）细狗行走动作，上半身不动；
   - （已处理）金克斯使用了透明通道，丢失阴影
   - （已处理）TODO：项目工程内batching失败（原因是目前ab方案导致每个模型都有一个独立的mat，不是共用mat，因此无法合批）
   - （已处理）TODO：动作资源打ab， 解决内存问题
   - （已处理，好像是有的，之前看起来硬切还是因为攻击动作丢失了，视觉看起来是硬切）crossfade无效，动作硬切
   
## 20231008 星期日
- （已处理。把循环方式从Once改成Loop就可以了，这是一中hack方式，理论上Once也应该可以的，攻击动作本来也应该是设置为Once类型，且使用预制件测试都没有问题，打成AB在项目中就出现了）TODO：攻击动作丢失，不显示
- （已处理）TODO：框架对材质球打AB的处理需要调整
- （已处理）TODO：game02 角色begin dead就不再移动


## 20231009 星期一
- 打AB工具升级，使得mat独立出来作为独立资源
- 重复文件检查工具
- TODO：还有些类型文件重复性未彻底处理干净

## 20231010 星期二
- （已处理，已使用字典）TODO：cache里面遍历资源的性能问题
- （暂时不处理）TODO：material加载后反复设置图片参数，需要规避
   - mesh renderer中material设置图片设计到引用计数。目前还不好拆分。

- （已处理）TODO：ShaderMeshAnimation的依赖处理和加载

## 20231011 星期三
- （已处理）TODO：支持RawImage的剥离
- （已处理）TODO：支持mesh的剥离

## 20231012 星期四
- 公司办公地点改到原511对面509。今早9点到达公司开始搬各自东西。
- （已处理。还是由其它fbx同名mesh导致的。）TODO：shawang 的mesh莫名旋转
- （已处理）TODO：对ab目标增加不处理标签功能
- （已处理）TODO：mesh依旧被打入到了prefab对应的ab中

## 20231013 星期五
- （已处理）：fbx过滤方式调整
- （已处理）TODO：剥离mesh，由于mesh collider会依赖到mesh,会导致mesh collider出问题，需要处理

## 20231016 星期一
- 对RawImage剥离的支持
- Texture和Sprite类型判断
- 解决meshcollider依赖mesh的问题
- （已处理）TODO：报错：CollisionMeshData couldn't be created because the mesh has been marked as non-accessible. Mesh asset path "" Mesh name "Garfield_changjing_03_beginning"

## 20231017 星期二
- 修复ZGame框架的几个小bug
- （已处理。因为emoji是挂在ui下的，而ui在DontDestroyOnLoad下，因此设置parent为null后也被放到对应场景下了）TODO:emoji回收的对象为啥显示在DontDestroyOnLoad下
- （已处理）TODO：RemoveRefTrs；AddRefTrs 判断逻辑有问题
- ``晚上9点05下班！！！``

## 20231018 星期三
- TODO：打包场景1的显示有问题，应该是shader变种的问题导致的


## 20231019 星期四
- （已处理）TODO：为资源收集器，增加开关属性，动态控制

## 20231020 星期五
- （已处理）TODO：Text、TMP多语言
- （已处理）TODO：图片多语言

## 20231023 星期一
- （已处理，已重写，之前就支持多平台的）TODO：头像模块重写。支持更多平台头像缓存

## 20231024 星期二
- TODO：目前客户端运行游戏有点卡，需要找到缘由
- TODO：调通资源的异步加载


## 20231026 星期四
- WebP图片支持


## 20231027 星期五
- 早晨忘了打卡，想起来已经9点31了。

## 20231030-20231031 星期一-星期二
- TODO：改成通过依赖的方式加载资源
- TODO：送礼tips UI调整
- 各种UI调整
- TODO：送礼tips，某个人连续送1个，没有X提示


## 20231101 星期三
- 今天把二江的房子退了，早晨喊了货拉拉把最后一波行李运送到了菁萃
- （已处理）TODO：礼物进阶功能
- （已处理）TODO：涛哥说的几个图标调整
- UNITY_IOS 和 UNITY_IPHONE 的区别

## 20231102 星期四
- TODO：框架支持节点数组 GiftTipArea.cs
- （已处理）TODO：在全名健身项目对当前项目框架调整


## 20231109 星期四
- （已处理）TODO：今天遇到奇怪bug，佳林那边新提交的几个礼物特效，对他们预制件打AB，由于我们剥离了AnimatorController，对AnimatorController打AB后，引用的.anim资源未打进去。


## 20231114 星期二
- Game02礼物调整
- Game02出生点逻辑调整

## 20231121 星期三
- 最近对Game02尝试做一些性能优化
   - 目前完成GPU蒙皮动画，使用官方AnimationInstancing方案（TODO：shader支持自发光，支持透明贴图；参考MeshAnimator，CrossFade使用jobs加速）
   - TODO：射线jobs:https://thegamedev.guru/unity-cpu-performance/raycast-commands/
   
- TODO:设置visual studio支持capolit
https://baiming.blog.csdn.net/article/details/129886670?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-129886670-blog-134344591.235%5Ev38%5Epc_relevant_default_base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-129886670-blog-134344591.235%5Ev38%5Epc_relevant_default_base&utm_relevant_index=2


## 20231124 星期五
- （已处理）TODO:bug,结算排行榜上的玩家（未加入对局）头像未显示问题
   - PlayerAvatarManager.cs L109
- （已处理）TODO:bug,热更后版本号未变
- （已处理）TODO:bug,快捷键无法结束游戏
- 出正式包1.0.1 resVersion:0
   - resVersion:升级到1，解决P快捷键仍然弹出问题
   - 重新出包了，还是从resVersion：0 开始
- cfg文件中AppBundleVersion和ResVersion容易混淆，目前是使用的AppBundleVersion。但是看家园确使用的ResVersion



## 20231128 星期二
- Timer框架调整，还有一些问题
   - Skill脚本中skillShootDelay
   - BulletAgent OnDestroy

## 20231129 星期三
- Timer新框架调整完毕：增加缓存机制，降低gc

## 20231201 星期五
- （已处理,设计如此）只有两个角色的情况下，出现细狗不打加菲，但加菲能打到细狗
- （已处理）只有一个角色推中间障碍，出现障碍移动速度大于角色行走速度
