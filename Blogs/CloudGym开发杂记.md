## 20231103 星期五
- 客户端框架升级
   - TODO：对scene的Root节点需求，后面可以去掉
   - TODO：holder异步加载
   
## 20231106 星期一
- 数据表设计
- demo快速验证

## 20231107 星期二
- 主播视频流，软件UI搭配问题讨论
- 确定本游戏Anim加载机制，并实现打包方案、播放逻辑
- 动作preview方案

## 20231108 星期三
- 调整数据表结构
- 核心流程30%

## 20231109 星期四
- 核心流程60%

## 20231110 星期五
- 动画播放核心功能（但是后续还需要测试一下，原始名字的clip被替换为一个其它名字的clip，那么后续这个节点再替换是使用最原始名字还是新名字）
   - [Unity中，通过代码创建overrideController并使用ApplyOverrides设置其参数](https://www.jianshu.com/p/166b1ab85e24)
   - [Unity Animator state节点的Motion动态替换AnimationClip](https://blog.csdn.net/qq_37776196/article/details/131300166)
   - 知乎好文[Unity中Animator Override的性能问题](https://zhuanlan.zhihu.com/p/371397382)
   
## 20231211 星期一
- TODO：集成 https://github.com/Tayx94/graphy ，以后这个插件永久集成到框架中