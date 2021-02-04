## 空包大小
笔者以安卓APK为例，使用Unity版本为Unity2019.4.0f1
Player Setting中``Scripting Backend使用默认的Mono``、 ``Api Compatibility Level使用.NET Standard 2.0``、 ``Target Architectures为ARMv7(由于使用的Mono，也只有ARMv7选项)``

打包APK后，包体大小为16.7MB。对其解压后，可以发现lib和assets目录占用了较大存储空间
- lib目录下为对应平台的.so文件
- assets目录下主要为相关DLL和资源文件

### 谈谈空包中的资源
有哪些资源被默认打入了包中，是一个值得分析的问题，因为资源一方面会影响包体大小、另一方面会影响软件的启动速度

资源文件主要是位于assets/bin/Data目录下的``unity default resources``和``data.unity3d``，占用空间约4MB

笔者使用[assetstudio](https://github.com/Perfare/AssetStudio)可以很方便分析出这两个资源文件都包含哪些东西

- 对于unity default resources

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210126192332.png)

可以看到这个文件主要包含Unity默认需要使用的或者可能会使用到一些东西，包括了Shader、Arial字体、Texture2D、Sprite、Mesh还有MonoBehaviour脚本

上述红框从上到下依次为：
- Arial字体
- 默认Mesh，因为开发者有可能会在运行时使用GameObject.CreatePrimitive()来生成对应物体
- Splash，默认启动界面
- Watermark，水印，比如常见的Development Build
- OnGUI中使用的图片资源

这个文件中要吐槽的点就是有些肯定不会用到的资源也被包含了，比如水印资源，把所有可能的都打进包体了，并没有做差分处理

- 对于data.unity3d
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210126200015.png)

这个文件包含的是Shader文件，而且量很大。对于这个文件让我联想到Graphics中的Built-in Shader Settings，二者有什么联系吗？

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210126200221.png)

上图为默认情况下的设置，有了解的童鞋麻烦告知一下二者的关联

## 启动资源加载