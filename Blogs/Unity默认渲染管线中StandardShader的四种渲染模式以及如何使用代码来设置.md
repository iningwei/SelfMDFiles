Unity默认渲染管线中StandardShader有四种渲染模式
## 渲染模式
- Opaque
不透明的渲染模式，适合诸如石头、砖块等不透明的物体

- Cutout
镂空的渲染模式，比如破布，剪纸。透明度不是0就是1，无半透明区域

- Fade
隐现的渲染模式，与Transparent的区别是高光反射会随着透明度而消失

- Transparent
透明的渲染模式，比如玻璃，水

## 渲染模式设置
编辑器下可以在面板中很方便设置，但是在代码层官方并没有提供直接的API，因此还需要通过mat的API进行间接设置，代码如下：
```csharp
public enum RenderingMode 
{
    Opaque,
    Cutout,
    Fade,
    Transparent,
}
 
public static void SetMaterialRenderingMode (Material material, RenderingMode renderingMode)
{
    switch (renderingMode) {
    case RenderingMode.Opaque:
        material.SetInt ("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.One);
        material.SetInt ("_DstBlend", (int)UnityEngine.Rendering.BlendMode.Zero);
        material.SetInt ("_ZWrite", 1);
        material.DisableKeyword ("_ALPHATEST_ON");
        material.DisableKeyword ("_ALPHABLEND_ON");
        material.DisableKeyword ("_ALPHAPREMULTIPLY_ON");
        material.renderQueue = -1;
        break;
    case RenderingMode.Cutout:
        material.SetInt ("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.One);
        material.SetInt ("_DstBlend", (int)UnityEngine.Rendering.BlendMode.Zero);
        material.SetInt ("_ZWrite", 1);
        material.EnableKeyword ("_ALPHATEST_ON");
        material.DisableKeyword ("_ALPHABLEND_ON");
        material.DisableKeyword ("_ALPHAPREMULTIPLY_ON");
        material.renderQueue = 2450;
        break;
    case RenderingMode.Fade:
        material.SetInt ("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.SrcAlpha);
        material.SetInt ("_DstBlend", (int)UnityEngine.Rendering.BlendMode.OneMinusSrcAlpha);
        material.SetInt ("_ZWrite", 0);
        material.DisableKeyword ("_ALPHATEST_ON");
        material.EnableKeyword ("_ALPHABLEND_ON");
        material.DisableKeyword ("_ALPHAPREMULTIPLY_ON");
        material.renderQueue = 3000;
        break;
    case RenderingMode.Transparent:
        material.SetInt ("_SrcBlend", (int)UnityEngine.Rendering.BlendMode.One);
        material.SetInt ("_DstBlend", (int)UnityEngine.Rendering.BlendMode.OneMinusSrcAlpha);
        material.SetInt ("_ZWrite", 0);
        material.DisableKeyword ("_ALPHATEST_ON");
        material.DisableKeyword ("_ALPHABLEND_ON");
        material.EnableKeyword ("_ALPHAPREMULTIPLY_ON");
        material.renderQueue = 3000;
        break;
    }
}
```