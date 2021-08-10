目前使用的是xLua，以该lua方案为例说明。
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

同时在Lua侧需要结合使用自带的nil和上述C#侧方法来进行判断，代码如下：
```csharp
---@param obj UnityEngine.Object
function IsUnityObjNull(obj)
    if obj==nil or obj:IsNull() then
        return true
    end
    return false
end
```

## 原因
1.事实上在Unity中当一个物体被Destroy或者未初始化的情况下，该对象在C#侧也不等于null。之所以等于null，是因为UnityEngine.Object重载了==操作符。可以通过System.Object.ReferenceEquals(null,obj)进行验证。
2.xLua将引用类型压栈时会将对象统一转换为object，这样导致Unity重载的==运算符失效。因此若lua中的GameObject被Destroy后，其引用变量obj不等于nil
3.Unity对部分内置的Component（如rigidbody、animator等），在GetComponent时，如果组件不存在，Unity不会返回null，而是返回一个会判断为null的object。因此在lua中若直接使用如下代码会有意料外的问题
```
 -- gameObject没有Rigidbody，但返回值不等于nil
    local old_rigidbody = self.Owner.gameObject:GetComponent(typeof(CS.UnityEngine.Rigidbody))
    if old_rigidbody==nil then
        --TODO
    end
```

## 参考文档
[xLua下调用GetComponent时返回值不是nil的坑](https://blog.csdn.net/gouki04/article/details/84784795)