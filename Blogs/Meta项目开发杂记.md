### 2022/09/20
1，kawaiicity中树到手机中面片shader有问题，shader变体要重新收集
(已解决)2，手机一开始场景烘焙贴图无法加载，测试是不是因为USE_BAKERY的宏没有添加的缘故
不是，还是代码错误导致的
(已解决)3，运行时依旧依赖bakery相关组件，需要把bakery文件夹拖到场景中，可以删除examples
不依赖，不需要任何bakery组件。出现问题是因为后处理节点的导致的，删除就没有出现了
4,光武做GameMonitorWindow
5,调整详细相关参数匹配立州的设置
（已解决）6，整理美术和程序工程，使MeshRenderer组件不存在显示上的问题``(是ZGame/Editor下脚本RenderOrder.cs导致的)``
（已解决）7，打包后缀带时间戳
8，摇杆第一次点击会消失
9，打包扩展：若宏有OriginLuaFile则报错，并终止打包
10，处理程序工程中报的系统日志： Render queue value outside of the allowed range (2450 - 2500) for selected Blend mode, resetting render queue to default
11，立州那边测出一些相机交互bug