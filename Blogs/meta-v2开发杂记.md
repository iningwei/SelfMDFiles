# 2023年12月29日    星期五
- TODO：把ShaderList移到ZGame内，或者提供一键生成ShaderList相关内容的工具
- TODO：字体设置

# 2024年1月2日    星期一
- （已处理）TODO：读表管理类，FileMgr考虑放到ZGame框架内
- 完善导表工具，支持根据平台设置自动导出bin文件到目标文件夹内


# 2024年1月8日    星期一
- （已处理）TODO：角色换成跳舞角色
- （已处理）TODO：打安卓包，外网跑下
- （已处理）TODO：场景换成skycity


# 2024年1月9日    星期二
- 核心流程重梭理 80%
- 完成昨天的TODO：角色和场景替换

# 2024年1月10日    星期三
- 本机安卓打包调通
- （已处理）TODO：一直以跑的动作进入另一个玩家范围，另一个玩家看不到其跑的动作

- （已处理）TODO：一些问题
   - A玩家网络断线后，B玩家屏幕上，A并没有消失
   - Actor.cs Init函数里面 realActorObj 和LoadOtherPrefab加载角色模型有点乱
   - 创角的时候把转向也带过来
   - Actor.cs L74,isReady的处理
   
# 2024年1月11日     星期四
- 晚上加班到11点40，故12日早晨10点前到

# 2024年1月15日     星期一
- （已处理）TODO：完善 PrintMessage，不用在每个消息单独打印
- （已处理）TODO：WebSocketManager，StarTopSocketManager要按照网络类型封装完善一下
- （已处理）TODO：socket消息receive的统一处理，统一打印


# 2024年1月16日     星期二
- （已处理）TODO：按照文哥那种节省服务器消耗的同步方式改后，角色方向始终为0，应该是客户端代码有问题

# 2024年1月17日     星期三
- 一些同步逻辑优化
- （暂时先这样）TODO：完善PB导表工具

# 2024年1月19日     星期五
- MD，早晨扛不住了海昌路去了下卫生间，早晨迟到了10多秒。炸了！！！



# 2024年1月23日     星期二
- 新主城场景集成
- 农场场景集成
- 场景跳转流程调试
- （已处理）TODO：之前进入主城场景后，上行 C2SRoleEnterSceneMap ，需要改成根据配置表的是否需要同步这个字段来控制


# 2024年1月24日     星期三
- 调通安卓热更功能，同时完善框架
- （已处理）Bug：农场退出后，关进程，再进入农场，通过农场进入到主城，回不到原始位置，且在回到主城后，服务端下发的位置信息无数据

# 2024年1月25日     星期四
- 废弃之前actor的一些代码配置，改成配置表读取
- 导表工具优化：\t分割符改成自定义复杂字符组合分隔符；支持excel字段不填写
- 客户端和服务端表统一：统一使用table目录下的表，客户端导表工具根据目录结构把需要表导入到导表工具的表工作目录。后续策划配表只需要考虑项目目录下的table目录即可
- TODO：jekens打包工具配置



# 2024年1月26日     星期五
- 元宇宙项目周报  1月22日 -> 1月26日  
1，新主城场景集成
2，农场测试场景集成
3，和服务端调试场景跳转逻辑
4，调通安卓热更新功能，同时完善热更框架
5，优化完善当前功能的表配置和代码逻辑
6，统一客户端和服务端表配置，策划只需从统一目录进行配置
7，导表工具优化：自定义分隔符；字段默认值
8，项目一键打包集成环境配置（30%）
9，和服务端制定农场模块表配置


# 2024年1月29日     星期一
- 一键打apk核心流程处理完毕：packVisualizeTool完善；包体信息文件
- TODO：通过jenkins传macros一键打apk
- （已处理）TODO：HotUpdateAssemblies设置失败
- TODO：农民模型和动作集成

# 2024年1月30日     星期二
- ConfigData表移除login相关控制，改到使用cfg文件和dynamic_cfg文件配合控制，更加方便快捷。

# 2024年1月31日     星期三
- 完善打热更资源工具
   - 同时提供专打工具和通过打包面板两种方式
   - 专打工具调整为继承自EditorWindow   
- （已处理）TODO：ignoreLogMsgIds通过cfg控制
- 通过打包面板修改cfg数据后，会重新加载读取，不再需要重新编译读取新数据了。
- TODO：android keystore还未配置
- TODO：热更资源自动上传到CDN；热更包自动上传到共享盘

# 2024年2月1日     星期四
- （已处理）TODO：安卓打包后，删除无用的两个文件夹


# 2024年2月2日     星期五
- 安卓包自动上传到共享盘
- 整理UI资源，制定图集分类。
- 拼主界面UI界面，抽取公用节点做成预制件
- （已处理）TODO：小地图逻辑


# 2024年2月5日     星期一
- 从光武那拿回家园模块，我这边开始弄。
- 实现操作台界面和逻辑

# 2024年2月6日     星期二
- （已处理）TODO：改表，slotid改成long类型


# 2024年2月20日     星期二
- 导表工具支持long和long[]类型
- 农场槽位安装角色
- 操作台在休息和开始工作两种状态下，机器人（工人）详情界面和相关逻辑
- （已提供）TODO：提供美术工程一键创建SpriteConfig的快捷方式
- 拼工作记录界面

# 2024年2月21日     星期三
- TODO：ShowNetMask和HideNetMask自动根据协议对来自动调用，需要扩展协议结构
- （已处理）TODO：图文混排
- 工作记录逻辑功能
- 开始工作，停止工作逻辑
- （已处理）TODO：ShowNetMask需提供参数，是否显示中间转动圈
- 解决一个空消息体导致的数据未处理的问题


# 2024年2月22日     星期四
- 图文混排
- 获得道具界面和逻辑

# 2024年2月23日     星期五
- 填充能量界面和逻辑
- （已处理）TODO：对实例化的item进行封装，类似area
- （已处理）TODO:window下的Area节点，增加标识，避免在window中查找子物体搜索到Area节点
- （已处理）TODO：底层支持图片置灰功能，需要改shader，通过materialpropertyblock来实现
   - TODO：UGUI不支持MPB，使用gray mat替换实现，需要测试mat卸载问题

# 2024年2月24日     星期一
- 客户端框架升级
   - 增加Node、NodeHolder概念，降低窗体冗余、耦合
   - 增加UIRootTag标识，处理AutoLinkUI 
   - 增加WindowMsgID概念，增加可读性
   
# 2024年3月4日     星期一
- 易宝支付：https://open.yeepay.com/docs/v2/products/yjzf/index.html


# 2024年3月6日     星期三
- 现在Unity打的apk都无法安装到模拟器上了。
Unity的TargetArchitectures只有ARM选项了，没有模拟器可用的X86了。是这个原因吗？
- 选定webview方案：UniWebView，明天带测试机来测试（PS：作者是OneVCat）
- 一个博文[【Unity】UniWebView的使用](https://blog.csdn.net/weixin_42186644/article/details/123506018)


# 2024年3月7日     星期四
- TODO：飞船表要增加scale字段，整体控制飞船大小
- TODO：测试挖矿场景渲染30个RT性能

- SDK相关要求
   - 美洽SDK安卓要求JDK7+
   - UniWebView要求安卓5.0（API Level21）以及以上、iOS 9.0以及以上
  
- 美洽SDK博文：https://blog.csdn.net/weixin_44554456/article/details/105584540


# 2024年3月8日     星期五
- 昨晚加班过了9点，今天9点50多打的卡
- 调通安卓上美洽客户SDK
   - 由于无法通过EDM4U顺利加载美洽安卓依赖，故目前采用导出android studio的方式进行打包


# 2024年3月11日     星期一
- 总结下之前角色同步相关的协议流程
```csharp
meta v2协议流程
## 大流程
1，http post请求拿到socket地址，C2SAccountLogin请求登录。收到登录成功返回（S2CAccountLogin），客户端请求自己的角色信息：C2SRoleInfo

2，收到自己角色信息：S2CRoleInfo，若RoleInfo.Level为0,则走创角流程3和4，否则走5

3，请求创角：C2SRoleCreate

4，收到自己角色信息：S2CRoleInfo；以及S2CRoleCreate告诉创角结果

5，根据S2CRoleInfo中的mapId，客户端表现上开始走进度条加载目标场景，场景加载完毕后，若场景需要同步角色信息，则还要告诉服务器客户端已经准备好了：C2SRoleEnterSceneMap。后续 6，7，8，9的流程也是场景需要同步玩家信息才会发生。


6，客户端依次收到：S2CRolePublic(玩家自己和玩家周围9宫范围的角色信息)后，开始创建角色。角色位置通过 S2CPosSync 同步过来。

7，游戏过程中，客户端会按照一定规则上行：C2SPosChangeAction 用来更新角色动作；C2SPosMove 用来更新角色位置和朝向

8，游戏过程中，有新玩家进入角色范围，服务器会下行：S2CRolePublic；玩家离开角色范围，服务器会下行：S2CPosRemove

9，游戏过程中，服务端会下行：S2CPosChangeAction、S2CPosMove以更新角色动作、位置、朝向


## 切换场景流程
1，客户端上行：C2SPosChangeMap
2，收到：S2CPosChangeMap 开始切换地图
3，若是切换到的目标地图是需要同步角色位置、方向信息到服务端的，则在客户端场景加载完毕后，需要上行 C2SRoleEnterSceneMap


## 其它
1，场景内服务器会主动下行的协议：S2CPlayerMin新角色进入玩家周围或角色基本信息(昵称、衣着等)更新; S2CSyncPos同步角色位置、朝向; S2CPlayerRemove移除角色（角色已不在玩家周围）;S2CChangeAction更新角色动作（走、跑、跳等）

2，场景内客户端会上行：C2SChangeAction用来更新角色动作；C2SPlayerMove用来更新角色位置和朝向

3，玩家主动切换地图场景：C2SChangeMap（PosId暂时没用上）
```
- mac机器配置开发环境
- （已处理）TODO：导表工具要增加遇到同名sheet，弹出提示的功能



# 2024年3月12日     星期二
- （已处理）TODO：mac cocoapods环境配置
- （已处理）TODO：mac vpn配置

# 2024年3月14日     星期四
- TODO:统一安卓和ios sdk接口名称
- TODO：ios通知unity sdk init成功，且接口和android保持一致
- TODO：和美洽咨询sdk 8.0的问题。有个断层，8.0直接到12.0。我们项目设置的最低是11.0。
   - 因此可否考虑把我们项目最低支持版本设置为12.0
   
# 2024年3月15日     星期五
- TODO：iOS向unity发送消息


# 2024年3月16日     星期六
- 今天过来加班
- （已处理）TODO：一键打所有窗口ab工具
- （已处理）TODO：导表工具，导出时，先删除tmp目录下所有文件
- （已处理）TODO：UniWebView插件.a文件svn未提交成功，导致iOS打包报错：Undefined symbol
   - 相关可以blog一下
   
   
   
# 2024年3月18日     星期一
- TODO：变体收集工具，需要排除当前渲染管线不支持的shader对应的mat。或者根据当前shaderlist的shader来筛选

- （已处理）TODO:iOS右下角trail version
- （已处理）TODO:去除unity启动图标
- iOS上有些页面中文乱码：原因是脚本里面的中文乱码了，是编码的问题，中文字符串代码在mac中已乱码
- （已处理）TODO：打包首次启动，若用的wifi，则未弹出网络使用的提示选择框，导致后续http请求无返回。目前解决办法是切到流量，强制弹出后即可。

- （已处理）TODO:登录 S2CAccountLogin 相关报错处理
- （已处理）TODO：MessageBox增加参数，控制显示几个按钮。主要用于LoginService登录错误，需要重启，只需要显示一个确认按钮。以及是否需要显示右上叉叉按钮


# 2024年3月19日     星期二
- 昨天加班到晚上9点过，今天上午9点50多打的卡
- TODO：从框架中移除lua相关
- TODO：客户端框架升级，适当降低 ShowWindow 传参的数量



# 2024年3月20日     星期三
- 昨天加班到晚上9点过，今天上午10点01打的卡
- TODO：新帐号系统逻辑


# 2024年3月21日     星期四
- TODO：iOS上测试热更新
- TODO：文哥HTTP协议改密码和注销帐号


# 2024年3月23日     星期六
- 今天加班
- 抗机器人验证
- TODO：重构窗体link这块的逻辑

# 2024年3月25日     星期一
- （已处理）TODO：iOS热更程序集调用DllImport的函数报错：Method body is null
   - 把通过 DllImport 调用iOS原生方法的接口统一移到SDKExt.cs中，并添加程序集SDKExt。然后设置程序集SDKExt.cs作为GameEntry程序集的引用程序集。
   - 本项目热更程序集为：Assembly-CSharp，其它程序集都为非热更程序集
   - 目前只找到通过把相关代码移到非热更程序集来解决的方法，导致的结果就是这些接口无法热更了。
- （已处理）TODO：解决iOS下GameEntry启动后，需要拉serverlist.json而此时网络未确权的问题
   - TODO：android上还要多测试几个机型，网络是否通使用的是unity的api，这个貌似在某些机型上不可用   
- TODO：断线重连的测试
- 实现PageView
- 解决nest scrollRect，外层不响应的问题


# 2024年3月26日     星期二
- Window和WindowManager重构，优化Linkwindow逻辑
- (已处理)TODO：测试下Linkwindow这块功能


# 2024年3月29日     星期五
- 热更新GameEntry一些逻辑处理：跳转下载界面
- 安卓keystore设置
- (已处理)TODO：白名单
- TODO：接入iOS支付


# 2024年3月30日     星期六
- 周六加班
- “我的”一级界面拼图
