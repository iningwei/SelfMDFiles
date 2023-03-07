## 环境
Entities：0.51.1-preview.21
Burst:1.6.6
Jobs:0.70.0-preview.7
Hybrid Renderer:0.51.1-preview.21

使用url:``com.unity.entities``和``com.unity.rendering.hybrid``即可安装上述所有包。

## 源码
```csharp
using System.Collections;
using System.Collections.Generic;
using Unity.Collections;
using Unity.Entities;
using Unity.Mathematics;
using Unity.Rendering;
using Unity.Transforms;
using UnityEngine;
using UnityEngine.Rendering;
using static UnityEditor.MaterialProperty;

public partial class CubeGameSystem : SystemBase
{
    protected override void OnCreate()
    {
        base.OnCreate();


        var manager = World.DefaultGameObjectInjectionWorld.EntityManager;

        var myCube = manager.CreateEntity(
            ComponentType.ReadOnly<LocalToWorld>(),
            ComponentType.ReadOnly<RenderMesh>()
        );
        manager.SetComponentData(myCube, new LocalToWorld
        {
            Value = new float4x4(rotation: quaternion.identity, translation: new float3(1, 2, 3))
        });
        var ah = Resources.Load<GameObject>("AssetHolder").GetComponent<AssetHolder>();
       
        var desc = new RenderMeshDescription(
          ah.myMesh,
          ah.myMaterial,
          shadowCastingMode: ShadowCastingMode.Off,
          receiveShadows: false);

        RenderMeshUtility.AddComponents(
         myCube,
         manager,
         desc); 
    }
    protected override void OnUpdate()
    {
 
    }
}
```

## 其它
运行时Entity创建后的可视化需要使用``RenderMeshUtility.AddComponents``这个API了，用诸如:
```csharp
entityManager.SetSharedComponentData(entity, new RenderMesh
{
    mesh = mesh,
    material = material,
    subMesh = 0,
    layer = 0,
    castShadows = UnityEngine.Rendering.ShadowCastingMode.On,
    receiveShadows = true
});
```
已经绘制不出来了。具体参见这里:[Runtime entity creation](https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@0.51/manual/runtime-entity-creation.html)

## 参考文档
1，译文：[基于 Game Object Conversion 和 SubScene 的 DOTS 开发工作流](https://zhuanlan.zhihu.com/p/109943463)， 5阿哥原文:[Game Object Conversion and SubScene](https://gametorrahod.com/game-object-conversion-and-subscene/)

2，[YakShaver:Unity DOTS编码实践：GameObject Conversion](https://zhuanlan.zhihu.com/p/267814302)

3，[YakShaver:Unity DOTS编码实践：序](https://zhuanlan.zhihu.com/p/266780584)

4，Yimi的ECS系列翻译文章 [Unity ECS 研究 01 —— 概念原理](https://yimicgh.top/%E7%BF%BB%E8%AF%91/ECS-01-Core/)

5，云风[浅谈《守望先锋》中的 ECS 构架](https://blog.codingnow.com/2017/06/overwatch_ecs.html)

6，ydwj[用Unity DOTS制作4万飞剑的太极剑阵！](https://auniquepig.com/2021/06/21/DotsSwords/)

TODO:https://blog.csdn.net/lvcoc/article/details/123094469 这个画不出来，使用RenderMeshUtility改一下看看

https://blog.csdn.net/qq_51773145/article/details/123358623