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

显示错误，错误原因是，箭头处未设置自发光贴图，把主贴图赋过去，最终效果和Built-in工程下的效果是一样的。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/BuiltInToURP/URPMatInspector.png)
