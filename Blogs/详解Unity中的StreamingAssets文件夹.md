## 前言
官方文档:https://docs.unity3d.com/Manual/StreamingAssets.html
这是一个只读、不可写的目录; 该文件夹下的资源会保持原始格式（比如图片不会被引擎进行纹理压缩）;dll文件或者脚本放在该文件夹下也不会参与编译。

官方推荐使用``Application.streamingAssetsPath``来获得该文件夹的实际位置，其可以规避平台差异。
对于Unity Editor，Windows平台，其等价于：``Application.dataPath+"/StreamingAssets"``
对于macOS，其等价于：``Application.dataPath+"/Resources/Data/StreamingAssets"``
对于ios平台，其等价于:``Application.dataPath + "/Raw";``
对于android平台，其等价于:``"jar:file://" + Application.dataPath + "!/assets/";``

补充：
对于android平台
- Application.streamingAssetsPath的详细格式形如："jar:file:///data/app/包名-XXXXX/base.apk!/assets" 亦即 "jar:file://"+Application.dataPath+"!/assets"
- Application.dataPath的详细格式形如："/data/app/包名-XXXXX/base.apk"
- Application.persistentDataPath的信息格式形如："/storage/emulated/0/Android/data/包名/files"

## 使用
### 读写
StreamingAssets文件夹在所有平台上都不支持写入。


对于非Android和WebGL平台，支持File或者Stream的同步读取。同时File或者Directory类的方法也都可用。
但对于Android平台和WebGL平台，对StreamingAssets目录使用File类，Direcotry类的方法，以及Stream方式的读取都不支持。
针对Android平台，如果有同步读取的需求，可以考虑在游戏运行时，把StreamingAssets下的文件写入persistentDataPath，后续的读取和写入都在persistentDataPath进行。另一种方法就是针对Android平台提供额外的jar,对StreamingAssets下的文件进行同步操作。
或者对Android平台和WebGL平台使用UnityWebRequest(使用WWW类进行读取已废弃)对StreamingAssets下的文件进行读取,该方法是异步的。对于其它平台UnityWebRequest也是支持的。

#### 使用UnityWebRequest读取
UnityWebRequest读取文件需要传入文件的URL，StreamingAssets目录不同平台对应的URL是不一样的，如下：
- Windows平台 file:///D:/DATA/StreamingAssets/data.json
- WebGl平台 http://localhost/StreamingAssets/data.json
- Android平台 jar:file:///data/app/xxx!/assets/data.json
- iOS平台 Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/xxx.app/Data/Raw

这时候最好通过构造Uri()的方式来规避平台的差异，获得真正的请求URL。如：
```csharp
var uri = new System.Uri(Path.Combine(Application.streamingAssetsPath,“data.json”)).AbsoluteUri;
```
方法Path.Combine(path1,path2)在检测到path1不是以分隔符结尾的话，会自动补充分隔符,



#### AB读取的特例
有一个特例，就是StreamingAssets目录下的AssetBundle资源，无论什么平台，都可以通过``AssetBundle.LoadFromFile(string path)``该接口（该接口亦可读取其它具体目录中的资源，不仅仅是StremingAssets目录）同步读取。
~~但是对于anroid平台，path参数需要使用路径（称之为路径1）``Application.dataPath + "!assets/XXXX"``的格式才能读取。~~
~~其它平台path参数使用路径（称之为路径2）``Application.streamingAssetsPath+"/XXXX"``即可读取~~
*（20220621补充：在较老版本Unity中，StreamingAssets目录下的AB资源若要通过AssetBundle.LoadFromFile进行读取，安卓平台上的路径确实需要使用Application.dataPath + "!assets/XXXX"(即上述删除线中提到的路径1)；但经笔者测试，在Unity2020.3.20f1版本中，路径1、路径2两种方式都支持；在Unity2021.3.3f1中只支持路径2的方式）*

#### Tables文件夹
笔者遇到过，在StreamingAssets目录下，创建文件夹名字为Tables，通过jar的方式读取其内的文件，无法读取。改成其它名字即可。暂时还不知道是什么原因，有知道的大佬告诉一下~~