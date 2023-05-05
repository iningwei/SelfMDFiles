As long as Unity doesent want to add this checkbox.

Just copy the shader from the URP package (Packages >> Universal RP >> Shaders), for example SimpleLit.

Choose a different name maybe.

Add this line of code:
```csharp
[ToggleUI] _CastShadows("Cast Shadows", Float) = 1.0
```

参考:[Shadows not casting with LWRP Lit Shader (Transparent Surface)](https://forum.unity.com/threads/shadows-not-casting-with-lwrp-lit-shader-transparent-surface.759827/#post-8181353)


其它方案（未测试）：
[URP打开半透明阴影unity技术分享](https://blog.csdn.net/dong2008hong/article/details/121648565)