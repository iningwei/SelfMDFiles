## 20230310 星期五
- 新游戏框架修改

## 20230312 星期日
``周日加班``
- 新游戏框架修改


## 20230313 星期一
- 新游戏框架修改
  - 跑通基础流程
  - 增加了6种兵种，实现相关攻击方式
  
## 20230314 星期二
- 完成GPU渲染动画的工作流测试

## 20230315 星期三
- 提审demo一些细化，包括出生点规则、一些AI逻辑、弹幕兵、礼物兵
- 性能上的一些问题总结
目前笔者电脑显卡RTX2060，CPU i5-10400F。StarTop和StarLike当蒙皮数量达到400，帧率即下降到20；
- TODO：噩梦之刃，双技能攻击
- TODO：出生点模型替换
- TODO：看帧同步相关文章：https://zhuanlan.zhihu.com/p/556920018
- TODO：场景增加四叉树管理，用于快速搜寻目标，不用逐个遍历


## 20230316 星期四
- TODO：炙刃战车喷火效果

## 20230317 星期五
- 增加技能系统，技能驱动子弹，子弹驱动buff
- 通过技能系统实现多种攻击类型

## 20230319 星期日
``周日加班``

- 增加6种兵种

## 20230320 星期一
- 增加6种兵种的子弹、死亡、击中等特效
- 增加碰撞，使用Navmesh的Obstacle Avoidance
- 增加激光类型子弹，并按照特定规则集成
- 弹幕兵通过配表来配置快捷键之类的
- 子弹目标S了，则自动干掉子弹，避免出现太多空枪
- TODO：A* Pathfinding相关教程：https://zhuanlan.zhihu.com/p/73145849
- TODO：https://halitanildonmez.wordpress.com/2018/01/11/using-a-path-finding-project-with-rvo2-library/

## 20230321 星期二
- demo优化完善
- 帧同步相关知识了解，寻路、碰撞，RVO
- TODO：http://www.jikedoc.com/article/4642614.html
- TODO：https://www.cxyzjd.com/article/KubilityDef/79483414

## 20230322 星期三
- demo提审前最后一些调整修改：新的角色入场提示界面，相机镜头调整
- TODO： [Sourcetree使用ssh后出现 The server's host key is not cached in the registry](https://www.cnblogs.com/ring1992/p/16589304.html)
- TODO：[Windows 平台 SourceTree 拉取仓库 反复崩溃](https://www.cnblogs.com/lesten/p/16202268.html)


## 20230323 星期四
``今天强哥、飒哥、静姐、飒嫂他们去北京出差``
- 移植GPU渲染蒙皮动画到异星项目
- 异星游戏内大量AddComponent造成很多GC，优化NavAgent和AnimAgent为非MonoBehavior的形式，降低GC。
- 确定新款demo改成回合制出兵，类似Game Science的产品《Red Tides》
- 开会讨论新项目网络版的寻路、avo进度以及否需要请外援
- 新款回合制demo 10%
- 解决GPU渲染框架一个bug，预制件放在场景中可以运行，但是拖拽到场景中的预制件看不到渲染内容。
原因是放在场景中的预制件名字和GPU Texture名字一致。拖拽到场景中的预制件名字后面自动加了(Clone)字符，导致有些资源加载不到。

- TODO：调研dots版本A*寻路
- TODO：学习A* Pathfinding Pro

## 20230324 星期五
- 讨论以一个客户端作为服务器，通过弹幕服务器转发指令和角色状态到另一个客户端的网络方案。因为角色量巨大，导致单点服务器网络带宽承受不了。遂作罢。
- 新款回合制demo 50%(核心功能实现)
- TODO：dots版本的A* ：https://github.com/jeffvella/UnityAStarNavigation
- TODO：一个demo 游戏的navmesh 与rvo动态避障算法（1）:https://blog.csdn.net/KubilityDef/article/details/79483414

## 20230326 星期日
``今天加班``
- 增加回合制表，配置每个回合的时间

## 20230327 星期一
- demo UI集成
- TODO：想办法把之前用了Find GetComponent这些API的地方改过来
- TODO：blog,背景跟随文字大小缩放，支持九宫
- TODO：异星的结算界面移植到新游戏
- TODO：增加demo的回合开始时出兵的提示功能


## 20230328 星期二
- DEMO优化
- 调通Animation Instancing支持武器的流程

## 20230329 星期三
- DEMO优化
- TODO：子弹拖尾延迟消失的需求

## 20230330 星期四
- 处理异星所有的角色，改成支持GPU渲染
- TODO：异星卡死问题
- TODO：异星打包工具，产品名设置问题
- TODO：对比大闹天宫录屏，技术验证
- TODO：做个一键上传本地客户端自动生成的日志的工具
- TODO：异星客户端逻辑更改，支持传统模式动画和gpu动画混合两种方式
- 出异星1.0.7.1版本包，解决1.0.7出现的主播客户端无响应的问题
- TODO：改了GPU后，结算的两个3D预制件节点有材质球丢失情况
- 解决异星虫族打破门、指挥官等非玩家召唤出来的单位无分数的问题
- TODO：注意测轰炸机不出来的问题
- 看dotsnav开源项目



## 20230331 星期五
- TODO：Power_Rocketeer_gpu原始模型需要调整才能支持双附件节点
- 调整异星的项目资源，以支持gpu渲染。

## 20230401 星期六
- ``周日我有事，因此今天加班，其他同事明天加班``
- 主要解决昨天的包积分不平衡的问题。
主要原因是昨天的包上错了，同时调整了伤害和胜利的系数

## 20230403 星期一
- 异星更新包1.0.8：点赞爆兵、新场景、平衡性调整等。整到4号凌晨2点才稳定下来。

## 20230404 星期二
- 出配合官方走直播伴侣的包，并微调数值和bug处理，版本号：v1.0.9
- 开会讨论新游戏：未来战场 美术风格需要改成偏卡通风格
- TODO：异星集成四叉树
- TODO：异星下个更新版本开发优先级
集成GPU渲染蒙皮动画
送礼，换阵营
场景加宽后的客户端调整
门破后人族白机甲可以出城的AI逻辑

## 20230405 星期三
- 今天清明节，为了周末能够双休，故今日过来上班
- 这个博客分享的一些代码很不错，有kdtree
https://gist.github.com/ditzel


## 20230406 星期四
- 卡通渲染方案研究
- TODO：考虑后续服务器拆分，抖音指令那边单独一个服务器；然后游戏逻辑至少2个服务器，一个用来测试一个用来线上跑
- TODO：考虑后续服务器停服时，记录当前正在开播直播间玩家已经送的礼物和分数。重启后为玩家加上分数和补上对应的礼物，这些礼物玩家可以通过特定指令来触发。


## 20230407 星期五
- kdtree研究，测试性能
- 异星新场景碰撞、路网设置
- TODO：C#下的窗体管理还是要做：完善相关工具链
- TODO：C#下的Resources目录下的预制件同步、异步加载


## 20230410 星期一
- 异星新场景调整，bug修复，出包等
- 项目调研：竞品远古战场
使用了RTS Toolkit Free插件
使用GPU Instancer插件来做蒙皮动画
Job来优化范围寻敌和最近敌人
重点关注：GameController3c.cs

- TODO：详细调研RTS Toolkit Free


## 20230411 星期二
- TODO：帧同步对战模式实现，还需要学习的流程
  - 学习jobs
  - 封装A*地图编辑器、高效算法实现
  - dots封装A*
  - RVO避障
  - 帧同步框架

- TODO：把StarLike寻路改成A* Pathfinding Pro.

## 20230412 星期三
- StarLike寻路换成A* Pathfinding Pro.


## 20230413 星期四
- TODO：研究立州发的BB哥之前做的星际之巅的客户端工程
- 研究A* Pathfinding Pro.实现分层，实现不同类actor穿越。
- TODO：研究Hybrid热更
- TODO：后续把U上传日志的功能去掉了，统一用工具上传

## 20230414 星期五
- A* Pathfinding Pro.寻路dots版，20%
- TODO：新项目的新UI集成
- TODO：一个很好的ECS视频教程：https://www.youtube.com/watch?v=IO6_6Y_YUdE
- TODO：深入理解JOBS：国人翻译：https://zhuanlan.zhihu.com/p/56459126 ,  官方文档：https://docs.unity.cn/Documentation/Manual/JobSystemOverview.html