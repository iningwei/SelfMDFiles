## 前言
官方文档:https://docs.unity3d.com/Manual/StreamingAssets.html
这是一个只读、不可写的目录; 该文件夹下的资源会保持原始格式（比如图片不会被引擎进行纹理压缩）
官方推荐使用``Application.streamingAssetsPath``来获得该文件夹的实际位置，其可以规避平台差异。
对于ios平台，其等价于:``Application.dataPath + "/Raw";``
对于android平台，其等价于:``"jar:file://" + Application.dataPath + "!/assets/";``

补充：
对于android平台
Application.streamingAssetsPath的详细格式形如：jar:file:///data/app/包名-XXXXX/base.apk!/assets
Application.dataPath的详细格式形如：/data/app/包名-XXXXX/base.apk
Application.persistentDataPath的信息格式形如：/storage/emulated/0/Android/data/包名/files

## 使用
### 读写
所有平台都不支持写入。
常规读取方法为UnityWebRequest(WWW已废弃),该方法是异步的。

对于非Android平台，支持File或者Stream的同步读取。同时File或者Directory类的方法也都可用。但对于Android平台，File类，Direcotry类的方法，以及Stream方式的读取都不支持。
针对Android平台，如果有同步读取的需求，可以考虑在游戏运行时，把StreamingAssets下的文件写入persistentDataPath，后续的读取和写入都在persistentDataPath进行。另一种方法就是针对Android平台提供额外的jar,对StreamingAssets下的文件进行同步操作。

#### AB读取的特例
有一个特例，就是StreamingAssets目录下的AssetBundle资源，无论什么平台，都可以通过``AssetBundle.LoadFromFile(string path)``该接口（该接口亦可读取其它具体目录中的资源，不仅仅是StremingAssets目录）同步读取。
~~但是对于anroid平台，path参数需要使用路径（称之为路径1）``Application.dataPath + "!assets/XXXX"``的格式才能读取。~~
~~其它平台path参数使用路径（称之为路径2）``Application.streamingAssetsPath+"/XXXX"``即可读取~~
*（20220621补充：在较老版本Unity中，StreamingAssets目录下的AB资源若要通过AssetBundle.LoadFromFile进行读取，安卓平台上的路径确实需要使用Application.dataPath + "!assets/XXXX"(即上述删除线中提到的路径1)；但经笔者测试，在Unity2020.3.20f1版本中，路径1、路径2两种方式都支持；在Unity2021.3.3f1中只支持路径2的方式）*

#### Tables文件夹
笔者遇到过，在StreamingAssets目录下，创建文件夹名字为Tables，通过jar的方式读取其内的文件，无法读取。改成其它名字即可。暂时还不知道是什么原因，有知道的大佬告诉一下~~