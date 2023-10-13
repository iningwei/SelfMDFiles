老的BuildIn渲染管线升级到URP后，问题最多的自然是材质球，编辑器已经内置了快捷方式转换材质球的shader（必须是Unity自带Built-in渲染管线下的Shader）。

通过Edit->Rendering->Materials->Convert Selected Built-in Materials to URP，可以自动抓换为对应的URP Shader。

需要注意的是转换后有些设置可能还需要手动调节。


下面以Legacy Shaders/Self-Illumin/Diffuse为例，看下转换流程。

模型在Legacy Shaders/Diffuse下效果（为了和Self-Illumin效果对比）：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/BuiltInToURP/LegacyDiffuse.png)


模型在Legacy Shaders/Self-Illumin/Diffuse下效果：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/BuiltInToURP/LegacyIlluminDiffuse.png)

转换后，在urp工程下效果：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/BuiltInToURP/URPErrorVisualResult.png)

显示错误，错误原因是，箭头处未设置自发光贴图，把主贴图赋过去，最终效果和Built-in工程下的效果是一样的：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/BuiltInToURP/URPMatInspector.png)

在官方文档[Converting your shaders](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@17.0/manual/upgrading-your-shaders.html)中有Built-in shader和URP shader对应关系的列表:

| Unity built-in shader | Universal Render Pipeline shader |
| ---- | ---- |
| Standard | Universal Render Pipeline/Lit |
| Standard (Specular Setup) | Universal Render Pipeline/Lit |
| Standard Terrain  | Universal Render Pipeline/Terrain/Lit |
| Particles/Standard Surface  | Universal Render Pipeline/Particles/Lit |
| Particles/Standard Unlit  | Universal Render Pipeline/Particles/Unlit |
| Mobile/Diffuse  | Universal Render Pipeline/Simple Lit |
| Mobile/Bumped Specular  | Universal Render Pipeline/Simple Lit |
| Mobile/Bumped Specular(1 Directional Light)  | Universal Render Pipeline/Simple Lit |
| Mobile/Unlit (Supports Lightmap)  | Universal Render Pipeline/Simple Lit |
| Mobile/VertexLit  | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Diffuse  | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Specular  | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Bumped Diffuse  | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Bumped Specular  | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Self-Illumin/Diffuse |	Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Self-Illumin/Bumped Diffuse | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Self-Illumin/Specular | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Self-Illumin/Bumped Specular | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Diffuse | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Specular | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Bumped Diffuse | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Bumped Specular | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Cutout/Diffuse | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Cutout/Specular | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Cutout/Bumped Diffuse | Universal Render Pipeline/Simple Lit |
| Legacy Shaders/Transparent/Cutout/Bumped Specular | Universal Render Pipeline/Simple Lit |