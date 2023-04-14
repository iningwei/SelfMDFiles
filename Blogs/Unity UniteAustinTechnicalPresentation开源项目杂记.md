```
Unity引擎在其2017.3版本中引入了C# Job System、Burst Compiler以及Entity Component System，使得开发者能够利用CPU的多核硬件并行处理游戏逻辑，大大提升了游戏效率。Nordeus Demo是Unite Austin会议上展示的通过利用上述技术实现了PC平台上数以万计角色同屏的效果。
```

1，源码:https://github.com/Unity-Technologies/UniteAustinTechnicalPresentation

包比较大有1.83G(TGA 2048的贴图很多，太占空间了)，笔者使用github通过各种方式都未能下载完成。可以考虑把github库迁移到gitee，再下载，另外gitee上已经有迁移好的该项目库：https://gitee.com/mirrors_Unity-Technologies/UniteAustinTechnicalPresentation ，通过gitee顺利下载。

2，注意事项：
- 若要顺利运行，必须安装Unity2018.1.0b12 Beta版本，下载地址：https://beta.unity3d.com/download/ed1bf90b40e6/public_download.html

- 修改manifest.json文件，删除其内所有内容，只留如下内容：
```
{
  "dependencies": {
    "com.unity.entities": "0.0.12-preview.2"
  }
}

```

- unity启动后若下载0.0.12-preview.2组件时报错``shasum check failed``, 笔者安装nodejs后，使用``npm cache clean --force``解决

3,解决ReflectionTypeLoadException

上述都没有问题后，打开NordeusDemo场景，运行会报错：
```c
ReflectionTypeLoadException: Exception of type 'System.Reflection.ReflectionTypeLoadException' was thrown.
System.Reflection.Assembly.GetTypes () (at <80e2a14930c247a2a3202625addf097a>:0)
Unity.Entities.DefaultWorldInitialization.Initialize (System.String worldName, System.Boolean editorWorld) (at C:/ProgramData/Unity/cache/packages/packages.unity.com/com.unity.entities@0.0.12-preview.2/Unity.Entities.Hybrid/Injection/DefaultWorldInitialization.cs:69)
Unity.Entities.AutomaticWorldBootstrap.Initialize () (at C:/ProgramData/Unity/cache/packages/packages.unity.com/com.unity.entities@0.0.12-preview.2/Unity.Entities.Hybrid/Injection/AutomaticWorldBootstrap.cs:11)
```
这时候需要定位到``DefaultWorldInitialization.cs``脚本的69行，和第40行的foreach内部代码一样，增加try catch代码段。需要注意的是该文件所在目录已经被设置为只读，需要先设置其所在目录可写，才能顺利修改。



4，PS:该项目的相机控制模块值得参考
```
中键滚动：缩放
右键按下拖拽：水平移动
中键按下拖拽：视角旋转
```