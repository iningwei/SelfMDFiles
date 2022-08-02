## C#
```csharp
public class Lesson5
{
​	public int RefOutFun(int a, out int b, ref int c) 
​	{ 
​		b = a;
​		c = b+c;
​		return 3;
​	}
}
```
## Lua
```lua
local  a1,b1,c1=CS.Lesson5().RefOutFun(2,4)
print(a1) --->3
print(b1) --->2
print(c1) --->6
local  a2,b2,c2=CS.Lesson5().RefOutFun(2)
print(a2) --->3
print(b2) --->2
print(c2) --->2
```

总结：out修饰的参数不传值，ref修饰的参数需要传值,ref类型的参数若不传值则使用默认值作为参数,第一个返回值是return返回的，其他是ref或out的数值 