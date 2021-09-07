## 情况说明
1，笔者的Unity版本是2020.3.0f1, 使用Unity默认老管线和Standard Shader。ForwardRendering实时渲染，没有烘焙。
2，所有的资源通过ab加载。，这边开发环境下编辑器使用的是android模式，加载的ab资源也是安卓下的ab，因此会出现GPU层shader的不兼容，我这边也是按照常规做法在客户端代码增加UNITY_EDITOR宏，在宏内为相应的材质球加载本地的shader。
3，目前的问题是：使用了Standard(Specular setup)和反射贴图的材质球关联的物体在美术场景编辑器内显示正常，在开发环境编辑器中（以及真机中）通过ab加载后反射效果异常，具体表现就是很黑。

## 问题排查
1，Shader变体不完整导致反射运算使用了错误的Shader
笔者通过在美术场景中注意开关材质球界面中的各种选项，可以确定完成了所有变体的搜集，但是依旧有问题。
且在美术场景中开关材质球中Standard(Specular setup)的一些选项（比如Forward Rendering Options下的Specular Highlights、Reflections）并不能出现和同样的异常效果，因此可以初步排除是Shader的问题
2，项目的环境光使用的是Skybox，因此也怀疑到是天空盒材质未生效导致的问题
但是通过在编辑器内查看Lighting->Environment面板，发现运行后相关天空盒是正确加载了的。
相关加载代码如下：
```csharp
        var mat = Resources.Load<Material>(matName);
        RenderSettings.skybox = mat; 
        DynamicGI.UpdateEnvironment();//must call this fuction,or skybox will not work corecctly
```

## 如何解决
最终发现还是Skybox导致的。笔者开发环境下Lighting->Environment->Skybox Material默认是没有设置天空盒的，虽然在运行过程中通过代码进行了加载，但是不知道为什么并没有对StandardShader的反射效果生效，目前还没有查到原因。
也因此在打包后真机上出现同样的问题。
在编辑器下通过设置一个默认的天空盒即可暂时规避该问题。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210907154426.png)