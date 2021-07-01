## 情况说明
1，笔者的Unity版本是2020.3.0f1, 使用Unity默认老管线和Standard Shader。
2，所有的资源通过ab加载。，这边开发环境下编辑器使用的是android模式，加载的ab资源也是安卓下的ab，因此会出现GPU层shader的不兼容，我这边也是按照常规做法在客户端代码增加UNITY_EDITOR宏，在宏内为相应的材质球加载本地的shader。
3，出现的问题是在编辑器下有些资源和其它资源之间的层级有问题，但是在手机上却是正确的。这对开发来说还是有一定的影响。

## 问题排查
1，排除了Shader变体导致的
2，后来突然发现出错的都是在Standard Shader中使用了Transparent渲染模式的物体。而且在编辑器下选择该物体，展开该物体种使用了StandardShader并设置了Transparent渲染模式的材质球的面板后，发现渲染层级又正确了
3，上述2的操作后，在编辑器中会有如下日志：Render queue value outside of the allowed range (2501 - 3999) for selected Blend mode, resetting render queue to default
4，由于编辑器在默认情况下的材质球时非展开状态，当出错的材质球展开后，又被自动设置回原始值（3000），因此并不知道renderQueue被设置为了多少
5，于是在代码层监听出错的mat，通过``mat.renderQueue``得到值为2000，是opaque模式的默认值

## 原因
个人猜测是在编辑器环境下Transparent渲染模式的RenderQueue没有被设置为正确的值导致了显示上的问题。在点击展开材质球时编辑器对参数进行了重设为默认值的操作，因此渲染问题消失。大概率是一个unity的bug

## 如何解决
对standard shader的材质球，若使用了transparent模式，则代码层设置一下rendererQueue
```csharp
if (shaderName == "My/Standard" || shaderName == "My/Standard (Specular setup)")
{
    if (mat.IsKeywordEnabled("_ALPHAPREMULTIPLY_ON"))
    {                            
        mat.renderQueue = 3000;
    }
}
```