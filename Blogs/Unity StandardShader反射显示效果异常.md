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
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210907154426.png)


## 如何解决
最终发现其实是自己对PBR的反射没有理解到位，工作流中缺少了一个关键步骤导致的-->需要反射探针（Reflection Probe）组件的配合才可以生效。
笔者使用实时渲染，但是又不想每帧都生成反射贴图，因此对探针选择的刷新方式为：Via scripting。通过调用组件的RenderProbe()方法即可完成渲染。需要注意的是渲染需要一定时间（根据硬件不同而不同），因此会出现模型视觉效果突变的情况，这个就需要用户结合使用IsFinishedRendering()方法自己在逻辑上处理了。伪代码可如下：
```csharp
 var renderId = probe.RenderProbe();
        TimerTween.Repeat(0.03f, () =>
        {
            if (probe != null && probe.IsFinishedRendering(renderId))
            {
				//TODO:
            }

           
        });
```

## 补充：使用Reflection Probe后在极少部分安卓机型上变黑的问题
笔者在13年机型三星note3中遇到。[reflection-probe-on-android-generates-black-images](https://forum.unity.com/threads/reflection-probe-on-android-generates-black-images.418445/) 这里有外国友人在Huawei MediaPad3 lite 上遇到过。

关闭Reflection Probe的HDR属性即可。部分移动设备不支持HDR。