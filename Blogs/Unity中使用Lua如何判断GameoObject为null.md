Unity中Destroy掉GameObject后，该GameObject则为null，但在Lua侧该GameObject并不等于nil。
在Lua中若要判断GameObject是否为null，需要在C#侧提供方法：
```csharp
public static class UnityEngineObjectExt
{
    public static bool IsNull(this UnityEngine.Object target)
    {
        return target == null;
    }
}
```

## 补充说明
事实上在Unity中当一个物体被Destroy或者未初始化的情况下，该对象在C#侧也不等于null。之所以等于null，是因为UnityEngine.Object重载了==操作符。可以通过System.Object.ReferenceEquals(null,obj)进行验证。