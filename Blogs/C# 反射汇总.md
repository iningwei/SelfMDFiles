- 根据类名字获得类的Type

```csharp
string className;
Type t=Type.GetType(className);
```

- 根据类ABCD获得类的Type
```csharp
public class ABCD{}
Type t=typeof(ABCD)
```

- 获得泛型类的Type
Type t=typeof(T);


- 获得类的构造函数，并调用
```csharp
//通过反射获得类
Type t = Type.GetType(windowName);
//获得所有构造函数
ConstructorInfo[] cMethods = t.GetConstructors();
for (int i = 0; i < cMethods.Length; i++)
{
    ConstructorInfo ci = cMethods[i];
    //参数
    ParameterInfo[] paramInfos = ci.GetParameters();
    Debug.Log("该构造方法名：" + ci.Name + ", 参数个数：" + paramInfos.Length);
    if (paramInfos.Length == 3)
    {        
        //调用构造函数                    
        target = ci.Invoke(new object[] { uiObj, windowName, false }) as Window;
        break;
    }
}
```

- 直接调用类的构造函数

```csharp
Type t = Type.GetType(className);
target = Activator.CreateInstance(t, new object[] { uiObj, windowName }) as Window;
```

- 数据类型转换
``Convert.ChangeType()``,原型 public static object ChangeType(object value, Type conversionType);
该方法并不属于反射相关接口，但是常用于反射相关工作中。

## dll处理
- 加载dll，并获得dll中的类
```csharp
byte[] buffer = File.ReadAllBytes(path);
var assembly = Assembly.Load(buffer);
foreach (var t in assembly.GetTypes())
{
    //类+有指定类型的基类
    if (t.IsClass && t.BaseType != null && t.BaseType.Name == typeof(Window).Name)
    {         
    }
}
```

## 需求
### 根据对象中某个字段的字符串名称，来改变该对象某个字段的值
```csharp
public class Bean
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Address;
}

var b = new Bean();
b.Name = "zcfly";
b.Age = 12;
b.Address = "China";
Debug.LogError("name1:" + b.Name + ", age1:" + b.Age + ",address1:" + b.Address);
var bType = b.GetType();
var nameP = bType.GetProperty("Name");
nameP.SetValue(b, "Jim", null);
var ageP = bType.GetProperty("Age");
ageP.SetValue(b, 333, null);
var addressM = bType.GetField("Address");
addressM.SetValue(b, "England");
Debug.LogError("name2:" + b.Name + ",age2:" + b.Age + ",address2:" + b.Address);
```


### 通过类名获得Type，并作为泛型方法参数
可能会考虑使用如下代码
```csharp
            string name = "JiDi"; 
            Type t = Type.GetType(name);
            actor=normalActorFactory.Create<t>(summonPlayer, comp, dataId, modelPath, initPos, initForward);
```
编辑器下报错：``“t”是 变量，但此处被当做 类型 来使用``

解决方案：
先通过反射获得对应的泛型方法，再通过泛型方法调用。代码如下：
```csharp
            string name = "JiDi";
            Type t = Type.GetType(name);
            MethodInfo method = typeof(NormalActorFactory).GetMethod("Create").MakeGenericMethod(t);
            actor = method.Invoke(normalActorFactory, new object[] { summonPlayer, comp, dataId, modelPath, initPos, initForward }) as Actor;
```
```csharp
public class NormalActorFactory
{
    public Actor Create<T>(Player summonPlayer, CompType comp, int dataId, string modelPath, Vector3 initPos, Vector3 initForward) where T : Actor
    {

    }
}
```

### 泛型方法中调用T的构造函数，最终返回T的实例
```csharp
    public T AddNode<T>(GameObject nodePrefab) where T : Node
    {
        // 使用 Activator.CreateInstance 创建 T 类型的实例
        T newNode = (T)Activator.CreateInstance(typeof(T), nodePrefab);

        // 返回新创建的实例
        return newNode;
    }
```


