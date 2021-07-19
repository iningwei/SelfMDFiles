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