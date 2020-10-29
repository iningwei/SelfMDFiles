- 根据名字获得类的Type

```csharp
string className;
Type t=Type.GetType(className);
```

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
