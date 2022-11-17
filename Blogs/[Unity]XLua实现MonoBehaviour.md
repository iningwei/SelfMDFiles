XLua作者已经提供了一个简易的实现示例：[U3DScripting](https://github.com/Tencent/xLua/tree/master/Assets/XLua/Examples/02_U3DScripting),核心思想是在C#侧提供一个MonoBehaviour类来驱动Lua侧的运行。

## 通过Lua表模拟
创建脚本 TestLuaBehaviour.lua
```lua
---@class TestLuaBehaviour
local TestLuaBehaviour = {}
---@type UnityEngine.Transform
TestLuaBehaviour.attachedTran = nil

---@param attachedTran UnityEngine.Transform
function TestLuaBehaviour.Awake(attachedTran)
    TestLuaBehaviour.attachedTran = attachedTran
    Log("awake:" .. attachedTran.name)
end

function TestLuaBehaviour.Start()
    Log("start")
end

function TestLuaBehaviour.Update(deltaTime)
    Log("update called,deltaTime:" .. deltaTime)
end

function TestLuaBehaviour.FixedUpdate()
    Log("fixed update called")
end

function TestLuaBehaviour.OnDestroy()
    Log("on destroy")
end

return TestLuaBehaviour
```

C# 侧创建驱动类 TestLuaBridgeBehaviour.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;
using XLua;

public class TestLuaBridgeBehaviour : MonoBehaviour
{
    Action<Transform> onAwakeFunc;
    Action onStartFunc;
    Action<float> onUpdateFunc;
    Action onFixedUpdateFunc;
    Action onDestroyFunc;

    LuaTable table;
    private void Awake()
    {
        table = ScriptManager.Instance.GetTable("TestLuaBehaviour");

        if (table != null)
        {
            onAwakeFunc = table.Get<Action<Transform>>("Awake");
            onStartFunc = table.Get<Action>("Start");
            onUpdateFunc = table.Get<Action<float>>("Update");
            onFixedUpdateFunc = table.Get<Action>("FixedUpdate");
            onDestroyFunc = table.Get<Action>("OnDestroy");

            this.onAwakeFunc?.Invoke(this.transform);
        }
        else
        {
            Debug.LogError("table is null");
        }
    }
    void Start()
    {
        this.onStartFunc?.Invoke();
    } 
    void Update()
    {
        this.onUpdateFunc?.Invoke(Time.deltaTime);
    }
    void FixedUpdate()
    {
        this.onFixedUpdateFunc?.Invoke();
    }

    void OnDestroy()
    {
        this.onDestroyFunc?.Invoke();
        if (table != null)
        {
            table.Dispose();
            table = null;
        }
    }
}

```

在Lua侧通过require获得TestLuaBehaviour表，并储存到全局变量TestLuaBehaviour中。然后为物体添加C#驱动类。
```csharp
TestLuaBehaviour=require "TestLuaBehaviour"
---@type UnityEngine.GameObject
local obj1=GameObject()
obj1:AddComponent(typeof(CS.TestLuaBridgeBehaviour))
```

Lua表模拟的缺陷是内存唯一性。因此如果有非唯一性的需求，需要对Lua表扩展为类的形式。

## Lua表升级为类
相关调整如下：
```lua
---@class TestLuaBehaviour
local TestLuaBehaviour = class("TestLuaBehaviour")

function TestLuaBehaviour:ctor()
    ---@type UnityEngine.Transform
    self.attachedTran = nil
end

function TestLuaBehaviour:SetAttachTran(tran)
    self.attachedTran = tran
    Log("tran name:"..self.attachedTran.name)
end

---@param attachedTran UnityEngine.Transform
function TestLuaBehaviour:Awake()
    Log("awake ")
end

function TestLuaBehaviour:Start()
    Log("start")
end

function TestLuaBehaviour:Update(deltaTime)
    Log("update called,deltaTime:" .. deltaTime .. ", objName:" .. self.attachedTran.name)
end

function TestLuaBehaviour:FixedUpdate()
    Log("fixed update called")
end

function TestLuaBehaviour:OnDestroy()
    Log("on destroy")
end

return TestLuaBehaviour

```

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;
using XLua;

public class TestLuaBridgeBehaviour : MonoBehaviour
{
    Action<LuaTable> onAwakeFunc;
    Action<LuaTable> onStartFunc;
    Action<LuaTable, float> onUpdateFunc;
    Action<LuaTable> onFixedUpdateFunc;
    Action<LuaTable> onDestroyFunc;

    LuaTable table;
    public void InitLuaBridge(LuaTable tb)
    {
        table = tb;
        if (table != null)
        {
            onAwakeFunc = table.Get<Action<LuaTable>>("Awake");
            onStartFunc = table.Get<Action<LuaTable>>("Start");
            onUpdateFunc = table.Get<Action<LuaTable, float>>("Update");
            onFixedUpdateFunc = table.Get<Action<LuaTable>>("FixedUpdate");
            onDestroyFunc = table.Get<Action<LuaTable>>("OnDestroy");

        }
        else
        {
            Debug.LogError("table is null");
        }

    }
    private void Awake()
    {
        this.onAwakeFunc?.Invoke(this.table);
    }
    void Start()
    {
        this.onStartFunc?.Invoke(this.table);
    }
    void Update()
    {
        this.onUpdateFunc?.Invoke(table, Time.deltaTime);
    }
    void FixedUpdate()
    {
        this.onFixedUpdateFunc?.Invoke(this.table);
    }

    void OnDestroy()
    {
        this.onDestroyFunc?.Invoke(this.table);
        if (table != null)
        {
            table.Dispose();
            table = null;
        }
    }
}

```

使用：
```lua
TestLuaBehaviour=require "TestLuaBehaviour"
---@type UnityEngine.GameObject
local obj1=GameObject()
obj1.name="obj1"
---@type TestLuaBehaviour
local obj1Behaviour=TestLuaBehaviour:new()
obj1Behaviour:SetAttachTran(obj1.transform)
obj1:AddComponent(typeof(CS.TestLuaBridgeBehaviour)):InitLuaBridge(obj1Behaviour)  

---@type UnityEngine.GameObject
local obj2=GameObject()
obj2.name="obj2"
---@type TestLuaBehaviour
local obj2Behaviour=TestLuaBehaviour:new()
obj2Behaviour:SetAttachTran(obj2.transform)
obj2:AddComponent(typeof(CS.TestLuaBridgeBehaviour)):InitLuaBridge(obj2Behaviour)  
```

可知修改后的TestLuaBridgeBehaviour.cs可以驱动各种类构造的lua特定结构。
注意：由于InitLuaBridge是在AddComponent后调用的，因此Awake没有触发。