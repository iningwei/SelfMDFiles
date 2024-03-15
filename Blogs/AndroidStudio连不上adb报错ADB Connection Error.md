之前笔者一直通过AndroidStudio来看日志，也一直用的一套自己的SDK，用了好几年了。

但是突然有一天，AndroidStudio启动后就弹出警告窗：ADB Connection Error，如下：

![ADBConnectionError](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/adb_connection_error.png?raw=true)


在Event Log面板还持续性的输出：Unable to detect adb version

![unableDetectADB](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/adb_unable_detect.png?raw=true)

检查了一下，我端口5037并没有被其它进程占用，是adb占用的。adb服务也是正常启动的。

``尝试删除AndroidStudio，重新安装，重新下载SDK，依旧同样报错信息。``

最终找到原因是：可能是更新了AndroidStudio，adb的版本不匹配。

## 解决方法
下载r28.0.0版本的platform-tools：
[platform-tools历史版本下载](https://androidmtk.com/download-android-sdk-platform-tools)

替换到SDK目录下的platform-tools文件夹下即可。

使用命令 adb version 打印内容如下：（ADB 版本是1.0.40）
```
Android Debug Bridge version 1.0.40
Version 4797878
Installed as E:\Softwares\Java\AndroidSDK-AndroidStudio2\platform-tools\adb.exe
```