Window->Rendering->Lighting Settings可以打开场景环境光的设置界面：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201119191043.png)

上图中红线处有三种模式可供选择``Skybox,Gradient,Color``
- Skybox对应的代码为：``RenderSettings.ambientMode = AmbientMode.Skybox;``

- Gradient对应的代码为：``RenderSettings.ambientMode = AmbientMode.Trilight;``

设置界面中选中Gradient后会出现三种颜色设置选项：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201119191423.png)
对应的代码为：
```csharp
RenderSettings.ambientSkyColor = new Color(,,);
RenderSettings.ambientEquatorColor = new Color(,,);
RenderSettings.ambientGroundColor = new Color(,,);
```

- Color对应的代码为：``RenderSettings.ambientMode = AmbientMode.Flat;``

Color选项的界面下有一个颜色Ambient Color设置，对应代码为``RenderSettings.ambientSkyColor = new Color(,,);``