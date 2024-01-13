# 2023年12月29日    星期五
- TODO：把ShaderList移到ZGame内，或者提供一键生成ShaderList相关内容的工具
- TODO：字体设置

# 2024年1月2日    星期一
- TODO：读表管理类，FileMgr考虑放到ZGame框架内
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

- TODO：一些问题
   - A玩家网络断线后，B玩家屏幕上，A并没有消失
   - Actor.cs Init函数里面 realActorObj 和LoadOtherPrefab加载角色模型有点乱
   - 创角的时候把转向也带过来
   - Actor.cs L74,isReady的处理