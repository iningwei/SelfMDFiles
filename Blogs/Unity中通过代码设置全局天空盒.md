传统的渲染管线中，Unity可以通过Lighting->Environment中设置天空盒，烘托一定的环境效果。

也可以制作好天空盒后，在运行时通过代码来设置，相关代码为：
```csharp
var mat = Resources.Load<Material>(matName);
RenderSettings.skybox = mat;
DynamicGI.UpdateEnvironment();//must call this fuction,or skybox will not work corecctly
```