### 2022/09/20
1，kawaiicity中树到手机中面片shader有问题，shader变体要重新收集
(已解决)2，手机一开始场景烘焙贴图无法加载，测试是不是因为USE_BAKERY的宏没有添加的缘故
不是，还是代码错误导致的
(已解决)3，运行时依旧依赖bakery相关组件，需要把bakery文件夹拖到场景中，可以删除examples
不依赖，不需要任何bakery组件。出现问题是因为后处理节点的导致的，删除就没有出现了
（已解决）4,光武做GameMonitorWindow
5,调整详细相关参数匹配立州的设置
（已解决）6，整理美术和程序工程，使MeshRenderer组件不存在显示上的问题``(是ZGame/Editor下脚本RenderOrder.cs导致的)``
（已解决）7，打包后缀带时间戳
8，程序工程以及打出的apk摇杆第一次点击会消失,是因为算位置不正确导致的
9，打包扩展：若宏有OriginLuaFile则报错，并终止打包
10，处理程序工程中报的系统日志： Render queue value outside of the allowed range (2450 - 2500) for selected Blend mode, resetting render queue to default
11，立州那边测出一些相机交互bug


### 2022/09/21
1，角色控制，动作增加融合
2，角色控制相机会出现偏的bug
3，角色180转向，增加渐变，预留参数
4，人物跳跃偶遇浮空不落地的问题
5，相机旋转过程中概率小幅回弹问题
6，Lightmap依赖处理
7，美术工程中角色控制相关调整要同步到程序工程
8，角色操纵，动画过渡融合的时间间隔需要提供可控参数接口

### 2022/09/22
1，走动的时候按跳跃人会僵直（拓展工具获得当前animator状态信息）
2，编辑器扩展：光武做两个工程文件互相复制

### 2022/09/23
1，加心跳协议，主协议0，子协议1，无消息体
2，config加配置，是否开启网络字节序处理
3，解决客户端运行时Render queue value outside of the allowed range的问题
4，总结一下cbCheckCode的原理