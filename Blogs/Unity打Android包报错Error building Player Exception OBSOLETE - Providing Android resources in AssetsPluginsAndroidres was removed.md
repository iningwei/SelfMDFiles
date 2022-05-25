对Unity工程进行了大版本升级，从2020.3.20f1升级到2021.3.3f1。
打包报错如下：
Error building Player: Exception: OBSOLETE - Providing Android resources in Assets/Plugins/Android/res was removed, please move your resources to an AAR or an Android Library. See "AAR plug-ins and Android Libraries" section of the Manual for more details.

## 原因
https://docs.unity3d.com/2021.2/Documentation/Manual/UpgradeGuide20212.html
Unity no longer copies Gradle
 project assets located in Assets/Plugins/Android/[res, assets] to the Gradle project.
- Previously you could place Gradle resources in this folder and Unity copied them into the Gradle project. You should now use AAR or androidlib plug-ins to pass additional Gradle resources to the application.
- If you place project assets in this folder, Unity shows a build error message.


![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220525194540.png)

## 解决
### Step1
查了一下工程中直接目录结构为Plugins/Android/res的有一个，用来存放FCM（FirebaseCloudingMessage）用到的缩略图和颜色值信息。在AndroidManifest.xml文件中有对它们的引用，如下：
```xml
    <meta-data android:name="com.google.firebase.messaging.default_notification_icon" android:resource="@drawable/icon_notification" />
    <meta-data android:name="com.google.firebase.messaging.default_notification_color" android:resource="@color/colorNotifyBg" />
```
另外还有两个目录结构形如：Plugins/Android/XXX/res的，分别是``Plugins/Android/FirebaseApp.androidlib/res``和``Plugins/Android/FirebaseCrashlytics.androidlib/res``
### Step2
经笔者测试，我们工程中只需要对第一种情况进行处理即可，解决方式就是把Plugins/Android/res下的资源打成aar，再把aar拖拽到Plugins/Android目录下即可。

 