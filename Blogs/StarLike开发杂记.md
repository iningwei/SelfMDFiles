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
