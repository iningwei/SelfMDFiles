在C#中这些关键字可能会在多种环境下被使用，本文简单介绍这3个关键字的使用场合，并重点比对它们作为参数修饰符时的使用区别和注意事项。
## in
在C#中，可以在下面这些地方使用in关键字：
```csharp
1、在泛型接口和委托的泛型参数中使用in关键字作为逆变参数，如：Action<in T>
2、作为参数修饰符
3、在foreach中使用in迭代
4、在Linq表达式中的join、from子句中使用in关键字
```
作为参数修饰符，in修饰的参数表示参数通过引用传递，但是参数是只读的，所以in修饰的参数在调用方法时必须先初始化！
调用时，必须满足下面条件：
```
1、传递之前变量必须进行初始化
2、多数情况下调用in关键字可以省略，当使用in关键字时，变量类型应与参数类型一致
3、可以使用常量作为参数，但是要求常量可以隐式转换成参数类型，编译器会生成一个临时变量来接收这个常量，然后使用这个临时变量调用方法
```

## out
在C#中，out参数可以用作：
```csharp
1、在泛型接口和委托的泛型参数中使用out关键字作为协变参数，如：Func<out T>
2、作为参数修饰符
```
作为参数修饰符，out修饰的参数表示参数通过引用传递，但是参数是必须是一个变量，且在方法中必须给这个变量赋值，但是在调用方法时无需初始化。
由于方法中必须要重新赋值，可知通过out调用的参数是没有必要初始化的。方法中通过使用out参数，解决了函数返回值只有一个的问题。
在调用时：
```csharp
1、必须声明out关键字，且变量类型应与参数类型一致
2、变量无需初始化，只需声明即可
3、如果不关注out参数的返回值，我们常使用弃元
```

## ref
在C#中ref用法较多，可以参考:[C#中ref关键字的用法总结](https://www.cnblogs.com/shanfeng1000/p/15043798.html)。
ref和out一样，可以解决函数返回值只有一个的问题。
ref和in、out的区别为：
```csharp
1、ref和in都是引用传递，而且要求调用方法前需要提前初始化，但是与in不同的是，调用时ref关键字不能省略，且参数必须是变量，不能是常量
2、ref和out都是引用传递，且在调用是，ref和out关键字不能省略，且参数必须是变量，不能是常量，但是ref要求调用方法前需要提前初始化，且无需在调用方法结束前赋值
3、与in和out不同的是，在调用方法中时，可以读写整个ref参数对象及它的成员
```

## in、out、ref的限制
C#中规定，引用传递（即in、out、ref）使用时有下面的限制：　　
```csharp
1、异步方法，即使用async修饰的方法中，参数不能使用in、out、ref关键字,但是可以在那些没有使用async关键字且返回Task或者Task<T>类型的同步方法中使用
2、迭代器方法，即使用yield return和yield break返回迭代对象的方法中，，参数不能使用in、out、ref关键字
3、如果拓展方法的第一个参数（this）是结构体，且非泛型参数，则可使用in关键字，否则不能使用in关键字
4、拓展方法的第一个参数（this）不能使用out关键字
5、如果拓展方法的第一个参数（this）非结构体，也非约束为结构体的泛型参数，则不能使用ref关键字
```

参考文档：
[C#中的值传递与引用传递（in、out、ref）](https://www.cnblogs.com/shanfeng1000/p/15043795.html)


