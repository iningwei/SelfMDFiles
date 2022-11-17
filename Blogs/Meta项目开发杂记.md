### 2022/09/20
（已处理）1，kawaiicity中树到手机中面片shader有问题，shader变体要重新收集
（已解决）2，手机一开始场景烘焙贴图无法加载，测试是不是因为USE_BAKERY的宏没有添加的缘故
不是，还是代码错误导致的
（已解决）3，运行时依旧依赖bakery相关组件，需要把bakery文件夹拖到场景中，可以删除examples
不依赖，不需要任何bakery组件。出现问题是因为后处理节点的导致的，删除就没有出现了
（已解决）4,光武做GameMonitorWindow
（已处理）5,调整详细相关参数匹配立州的设置
（已解决）6，整理美术和程序工程，使MeshRenderer组件不存在显示上的问题``(是ZGame/Editor下脚本RenderOrder.cs重绘MeshRenderer导致的)``
（已解决）7，打包后缀带时间戳
（已处理）8，程序工程以及打出的apk摇杆第一次点击会消失,是因为算位置不正确导致的
9，打包扩展：若宏有OriginLuaFile则报错，并终止打包
（已解决）10，处理程序工程中报的系统日志： Render queue value outside of the allowed range (2450 - 2500) for selected Blend mode, resetting render queue to default
（已处理）11，立州那边测出一些相机交互bug

### 2022/09/21
（已处理）1，角色控制，动作增加融合
（已处理）2，角色控制相机会出现偏的bug
（已处理）3，角色180转向，增加渐变，预留参数
（已处理）4，人物跳跃偶遇浮空不落地的问题
Kawaii场景部分碰撞体的MeshCollider设置了Convex导致的
（已处理）5，相机旋转过程中概率小幅回弹问题
``未找到原因。通过取消旋转damp的方式hack``
（已处理）6，Lightmap依赖处理
（已处理）7，美术工程中角色控制相关调整要同步到程序工程
（已处理）8，角色操纵，动画过渡融合的时间间隔需要提供可控参数接口

### 2022/09/22
（已处理）1，走动的时候按跳跃人会僵直（拓展工具获得当前animator状态信息）
（已处理）2，编辑器扩展：光武做两个工程文件互相复制

### 2022/09/23
（已处理）1，加心跳协议，主协议0，子协议1，无消息体
（已处理）2，config加配置，是否开启网络字节序处理
考虑后不再添加配置
（已解决）3，解决客户端运行时 Render queue value outside of the allowed range 的问题
4，总结一下cbCheckCode的原理

### 2022/09/26
（已处理）1，角色控制状态化
2，回看之前悟空传同步方案
玩家进入、创建玩家： ModelService.cs,ActorModelACK 玩家自己和其它玩家

位置同步相关：
玩家自己
SceneService.cs:L178 进入场景后，向服务端发送ClientReadyACK
SceneService.cs： PositionACK 为服务端下行位置同步
SceneService.cs:L241 SendPlayerPos 为客户端上行位置
ActorMainCityFlyAction.cs 飞行
其它玩家



### 2022/09/27
1，大地图分块加载技术调研：分块的导航网格如何处理、分块的烘焙贴图如何处理、分块的角色信息同步如何处理
2，ab异步加载，资源的小状态是否需要设置标识？
（已处理）3，打包动态设置宏，编译监听
4，测试场景切换烘焙资源的卸载情况
（已处理）5，测试场景additive时，烘焙资源的机制
（已处理）6，处理ab 先异步加载，再立即调用同步加载代码 


### 2022/09/28
1，查看内存中为啥shader占用这么多
（已处理）2，处理同一帧：先异步加载某个ab，再同步加载某个资源的问题
（已处理）3，重构ab同步加载，改成callback方式
（已处理）4，ShowWindow和ShowWindowAsync 回调代码统一
（已处理）5，ABSprite、ABTexture，同步、异步回调的代码归一化。ab unload时机。

### 2022/09/29
（已处理）1，ABPrefab、ABSprite等通过统一接口加载资源，不再分别提供同步异步两个接口了。
（已处理）2，Editor下运行没有问题，但是安卓机上有问题，包括lightmap一直不显示（没有相关报错信息）
原因是客户端工程改了Res相关类的结构，但是美术工程没有同步修改，导致真机上出问题。。


### 2022/09/30
（已处理）1，场景切换、多场景加载，及涉及到的lightmap index更新。
（已处理）2，特别要注意一键打Scene AB时，RootCompInfoHolder数据为空的情况，导致的场景贴图异常。考虑把打Scene AB拆分为两个步骤。

### 2022/10/08
（已处理）1，双端代码同步一下
2，本地化语言初始配置在cfg文件，用户可在app内设置，存储到PlayerPrefs中。相应调整。
3，增加SKIP_SPLASH宏
（已处理）4，分析舞吧项目换装编码规则，动作，喊佳林调整模型结构；和主策制定初始模型命名规则
```
编码格式  
0_0_0000_0 未使用的占位则不显示

第1位代表层次编码：共1-9，越外面的标识越覆盖在蒙皮上层，同层次不产生覆盖关系，第一期不用处理。  
第2位代表部件流水序号，与层次编码一起组成了部件  
第3-6位代表了服饰流水编号  
第7位代表了性别，0为通用，1为男，2为女
```
5，写工具检测模型命名、贴图命名是否符合规范

### 2022/10/09
1，ABManager中加载common ab资源后，不能对其使用ab.Unload(false),卸载。否则在手机端会出现shader全部丢失的问题。在PC上没问题。
2，增加对TextMeshPro组件的依赖引用关系，避免其shader丢失。
3，今天打包又遇到在安卓真机上lightmap最终设置后场景变黑的情况了。然后过了一段时间打包又突然好了。目前还没有定位缘由，猜测原因：a,打包时部分资源没有加载进去，导致最终lightmap显示有问题。因为笔者这边没问题的包的包体尺寸要大于有问题的包的包体。b,这次又是美术工程和程序工程都新增加了compinfo，但是没有重打ab包。
4，提供给佳林：换装蒙皮或者模型命名工具
5，目前kawaiicity场景在PC上有两个性能问题：
a:程序运行保持无任何交互，每隔一小段时间会有一个CPU的尖峰，查看CPU Profiler是Gfx.WaitForPresentOnGfxThread。而这个参数通常意味着是GPU的问题。
b:场景异步卸载时，由于OnObjDestroyNotice导致的EarlyUpdate.UpdatePreloading很耗时。
从2000ms->690ms(解决了AB引用之前遗留的bug)->300ms(Res内维护一个dic，记录Trans的ID，提高从Res查找引用Trans的效率)


### 2022/10/10
（已处理）1，AB依赖问题处理
（已处理）2，OnObjDestory处理
3，Lightmap texture ab load比较耗时，目前看是每个图耗时2.4秒
4，协程工具考虑换成这个：[MEC](https://assetstore.unity.com/packages/tools/animation/more-effective-coroutines-free-54975#description)，教程：http://trinary.tech/category/mec/
并考虑上传到网盘和csdn。



### 2022/10/11
（已处理）1，TextMeshPro支持中文，打ab要检测TextMeshPro不能使用默认字体。
（已处理）2，日历选择[ZCalendar](https://gitee.com/jacobkay/unity-calendar)
（已处理）3，城市选择方案
计算城市距离：https://blog.csdn.net/weixin_43881394/article/details/109616884
（已处理）4，星座方案：直接走配置
描述走配置，其它在C# TimeTool.cs封装好
（已处理）5，调整pbr shader：漫反射、法线、自发光、混合贴图（R:金属度，G:AO，B:粗糙度）
使用该方案：[UnityShader 基础（34）-综合应用-PBR材质](https://zhuanlan.zhihu.com/p/422881152)
参考：[Unity PBR Standard Shader 实现详解（一）PBR的简单介绍及美术原理](https://zhuanlan.zhihu.com/p/135874778)


### 2022/10/12
（已处理）1，客户端导表问题
已修改导表工具，和服务端公用一张表
（已处理）2，服饰模型编号调整，后期还需反馈给佳林
3，编号或者表还要增加配置，用来确定模型是通过蒙皮还是挂载点的方式加载到角色身上
（已处理）4，看佳林 那边总结的换装方案插件
（已处理）5，和佳林对接DynamicBone的实现


### 2022/10/13
1，avatar 扭曲骨骼 twistbone处理：https://blog.csdn.net/qq_21397217/article/details/120896318
（已处理）2，dynamicbone骨骼方案确定
3，APowerREC,Araxis软件整理上传
（已处理）4，AutoLink工具对于按钮的点击事件自动注册，自动生成函数，自动移除注册
经考虑，不自动注册点击事件，由程序自己处理
（已处理）5，和潘老师那边加上登录前的http请求获得服务器列表

6，佳林 推荐[Magica Cloth](https://www.bilibili.com/video/BV1iL4y1i7p5/?spm_id_from=333.337.search-card.all.click&vd_source=35717917bbfc5fd6004dd7231be24c25)系统。
相关教程：
[Magica Cloth 布料模拟使用心得，以及插件功能介绍](https://www.163.com/dy/article/FLBHM90H0526E124.html)
[DynamicBone（动态骨骼）MagicaCloth的优化策略（4）](https://zhuanlan.zhihu.com/p/526285709mei'you)

### 2022/10/14
（已处理）1，loading加载转场界面，以及相关底层逻辑处理
2，刘海屏，水滴屏，安全区处理
3，异步打开窗体，刚好窗体中有DynamicComp引用的贴图，在增加引用计数时，可能窗体中的贴图还没有加载好。导致问题。
（已处理）4，lua中使用rapidjson解析json数据
5，xlua第三方库封装记录一下


### 2022/10/15
（已处理）1，astra换装方案调研：https://assetstore.unity.com/packages/3d/characters/astra-232906
该方案类似于SDK的方式，需要注册获取相关key
fist name:hui
last name:zhou
email:215598028@qq.com
password:zhouhui123@@@

该方案，也是通过直接换蒙皮的方式，并没有太复杂的处理人物模型，包括捏脸，捏身体。

（已处理）2，测试Magica Cloth模拟头发效果
效果比DynamicBone要好，裙子可能要用Deformer的方式，但是该方式相对较耗，还未测试。


### 2022/10/18
（已处理）1，3D音效资源消耗情况研究
Unity已内置支持
（已处理）2，高德地图api封装
（已处理）3，星座api封装
（已处理）4，张峰瑜获取城市的坐标直接存储在城市xml中，避免后续用户异步获取繁琐
5，角色位置同步协议联调

### 2022/10/19
1，佳林有个模型的动作（Humanoid）握拳握不住，分析一下原因

### 2022/10/20
（已处理）1，中文字体部分乱码
（已处理）2，和张瑞宇对接角色shader，进一步完善之

### 2022/10/21
（已处理）1，通过测试Role跳起用时，和各个log期间的高度，来查找程序工程中角色跳起到落地时间略短的原因
原因：程序工程中调用HandleMove()，在RoleJumpAction.cs中没有放到FixedUpdate中调用。
2，总结下TextMeshPro中文乱码的解决方案

### 2022/10/24
（已处理）1，看新的角色shader
（已处理）2，张峰瑜调整ExcelTool

### 2022/10/25

### 2022/10/26


### 2022/10/27
（已处理）1，调整服饰模型命名规则：
000_0000
第1位代表层次编码：共1-9，越外面的标识越覆盖在蒙皮上层，同层次不产生覆盖关系，第一期不用处理。
第2位代表部件流水序号，与层次编码一起组成了部件（比如上衣、裤子）。
第3位代表基于部件的细分编号，比如1，2（不超过9）
1-3位就组成最终服饰类型（比如T恤、卫衣）
1-3位，可以直接参考section表的id字段。

4-7位代表不同服饰类型下的编号，编号从1到9999。这4位中未占用的0可以省略不显示。


（已处理）2，Lua工程窗体的文件件移除Window后缀，保持和美术工程中的文件夹名称一致
（已处理）3，Avatar RenderTexture渲染，旋转
（已处理）4，创角基础模型展示(根据穿戴信息填充对应部位)
（已处理）5，角色基础模型上的基础部件，需要添加 DynamicCompInfoHolder



### 2022/10/28
（已处理）1，Avatar系统开发
（已处理）2，出安卓包，测试性能

### 2022/10/29
``周六加班``
（已处理）1，分别在CSharp侧和lua侧，测试测嵌套结构
（已处理）2，proto文件加消息号，自动导出处理
由于有大量空消息，还是要手动添加，故不处理
（已处理）3，ScrollView.cs考虑加入onItemLostRender
（已处理）4，proto导lua工具重写

### 2022/10/31
（已处理）1，lua侧，MsgID注册工具
（已处理）2，ScrollView组件，修改Instantiate为通用带引用计数的实例化方式


### 2022/11/1
（已处理）1，增加SkinedMeshRenderer对骨骼节点依赖的脚本，代码中需要设置smr.bones，否则其不可见

### 2022/11/2
（已处理）1，导出的proto.lua要添加注释信息

### 2022/11/3
1，cfg增加language项，启动后自动写入到 PlayerPrefs


### 2022/11/4
（已处理）1，PM工具
2，PlayerPrefs扩展
https://github.com/sabresaurus/PlayerPrefsEditor
https://github.com/Dysman/bgTools-playerPrefsEditor    https://assetstore.unity.com/packages/tools/utilities/playerprefs-editor-167903

### 2022/11/7
（已处理）1，集成跳舞角色性能测试
一个蒙皮+常规动作+创建的角色使用跳舞动作
2，物品时效性的统一处理

### 2022/11/8
1，亟待解决Coroutine造成的大量GC问题。废弃MEC方案。
2，确定大场景加载方案
3，LoadingWindow进度感觉不太对
4，确定场景使用什么shader
5，ovo pbr shader的 暗色 这个参数 没效果，处理一下
6，处理大量texture加载时产生的gc alloc
（已处理）7，通用的GM命令工具集成
（已处理）8，集成MapID

### 2022/11/9
（已处理）1，刘海屏、水滴屏等异形屏适配
2，瑞宇新shader集成
（已处理）3，处理TMP的描边问题
使用material-preset,限制描边类型差异数量
4，佳林那边人嘴巴不动，找原因


### 2022/11/11
（已处理）1，跳舞战斗，进场相关流程
（已处理）2，玩家actor同步相关改成Lua侧实现


### 2022/11/12
``周六加班``
1，研究下UMA插件 https://www.bilibili.com/video/BV1gA411u7UF/?spm_id_from=333.337.search-card.all.click&vd_source=35717917bbfc5fd6004dd7231be24c25


### 2022/11/14
（已处理）1，luaEnv tick
2，测试新角色部件
3，整理Lua侧Monobehavior实现机制
（已处理）4，看下LODGroup的原理，和使用流程

### 2022/11/15
1，确定目前不用LOD
会增加包体；会增加大量烘焙贴图

2，捏脸、表情相关资料
[骨骼动画系统——捏脸方案的实现与介绍(八)](https://zhuanlan.zhihu.com/p/466851975)
[Unity 人物捏脸的实现](https://www.jianshu.com/p/0a9ad1e03992)
[Unity3D捏脸+衣服摆动技术介绍](https://blog.csdn.net/jxw167/article/details/101711055)

3，unity插件
[Mesh Deformation Full Collection](https://assetstore.unity.com/packages/tools/modeling/mesh-deformation-full-collection-47061)

### 2022/11/16
（已处理）1，调研 TMP 描边方案
最终决定使用MaterialPreset的方式

### 2022/11/17
（已处理）1，换新女模型
（已处理）2，确定服饰还要单独出一套不带身体部分的模型，供3D展示时使用
3，遇到一个bug，有概率在创角界面，还没有创角就收到了playermin的下行。后续遇到再跟踪一下缘由。
4，TMP还是支持文字渐变色的