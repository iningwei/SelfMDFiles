在Unity中使用URP时，会有需求使用自定义的一些shader来实现特殊效果，这时如果我们直接使用新建材质与无光照着色器（Unlit shader），可能会发生一个对于新手而言意料之外的问题——物体！消失了.



## 解决办法：
打开你正在使用的的通用渲染器（Universal Renderer Data），找到 Rendering -> Depth Priming Mode ，将其从默认的“Audo”修改为“Disabled”。

 


## 如果你的问题解决了并且想知道为什么的话，请接着往下阅读。
什么是深度引动模式？深度引动技术是Unity2021.2中URP管线的新增功能。在Unity的文档中是这样解释的“The feature uses a depth prepass to determine which pixel shader invocations Unity canskip”，通过使用一种用于预处理深度信息的Pass来帮助Unity跳过对部分片元的渲染。而在使用Unity2021认的URP项目时，其自动生成的通用渲染器文件（Universal Renderer Data）会默认开启这一项技术，而也是问题的根源。

为什么深度引动模式会导致我们的物体消失？首先必须澄清的是，这并不是Unity的什么Bug，而是很典型的手效应事件，对不熟悉技术的使用导致了意料之外的错误。深度引动模式需要使用一种LightMode为DepthOnl的特殊Pass来生成场景中不透明物体的深度图，而我们所创建的无光照着色器是不具有该类型Pass的（因为根就没写这东西），这就导致在开启了该技术的渲染流程中，这些使用了“不具备DepthOnlyPass的shader”物体无法生成深度信息从而被Unity过早剔除无法进行后续的渲染，造成了物体的消失。

因此若想在URP项目中同时使用深度引动技术与自定义shader，则务必注意需要在自定义shader中向Unity明DepthOnlyPass用于执行深度引导。幸运的是Unity在Universal Render Pipeline内置的Unlit shade中已经实现了该Pass，在自定义shader中使用以下语句即可直接使用。
```c
UsePass "Universal Render Pipeline/Unlit/DepthOnly
``` 



## Tips：
- 深度引动模式只工作在前向渲染的情况下，因此在通用渲染器中为你的项目选择延迟渲染路径的话就能绕过这个问题。
- 深度引动模式只针对不透明物体进行优化，因此如果你将物体的渲染队列设置为“透明”，那么即使不添加DepthOnlyPass也能正常渲染，不过代价是什么呢？
- 更多参考资料可以查看[官方文档](https://docs.unity.cn/Packages/com.unity.render-pipelines.universal@13.1/manual/urp-universal-renderer.html)。