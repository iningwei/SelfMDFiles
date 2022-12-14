官方文档看这里：[Unity Remote](https://docs.unity3d.com/cn/2021.2/Manual/UnityRemote5.html)。
笔者以Android为例，只需要注意几个设置对了就可以顺利连上Remote。
1，下载安装[Unity Remote 5](https://play.google.com/store/apps/details?id=com.unity3d.mobileremote)
2，Project Settings->Editor->Unity Remote下的Device要选择Any Android Device
3，Editor->Preferences下要配置好Android SDK
4，安卓设备设置为开发者模式且开启USB调试
5，通过USB线连接后，确认adb连接上（有些线只有充电功能，无法连接adb）
6，启动手机上的Unity Remote应用后，编辑器上点击Play按钮
