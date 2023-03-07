## 创建myCube Entity时增加Translation组件和CubeTag
```csharp
public partial class CubeGameSystem : SystemBase
{
    protected override void OnCreate()
    {
        base.OnCreate();
 
        var manager = World.DefaultGameObjectInjectionWorld.EntityManager;

        var myCube = manager.CreateEntity(
            ComponentType.ReadOnly<Translation>(),
            ComponentType.ReadOnly<CubeTag>(),
            ComponentType.ReadOnly<LocalToWorld>(),
            ComponentType.ReadOnly<RenderMesh>()
        );
       //...省略
}
```

```csharp
public class CubeTag : IComponentData
{
   
}
```

## 改变Translation值使Cube动起来
```csharp
public partial class CubeBouncingSystem : SystemBase
{
    protected override void OnUpdate()
    {
        int multiplier = Input.GetKey(KeyCode.Space) ? 33 : 12;
        var elapsedTime = Time.ElapsedTime;

        Entities.WithAll<CubeTag>().ForEach((ref Translation t) =>
        {
            float3 value = t.Value;
            value.y = math.cos((float)elapsedTime * multiplier);
            t.Value = value;
        }).ScheduleParallel();
    }
}
```