## 前言
编辑器上查看Profiler只能粗略分析性能状态，游戏的实际消耗还需要借助真机进行。
android手机连接Profiler，一般有两种方式Wifi和USB，笔者使用的是后者。
笔者Unity环境：Unity2019.4.2f1
## 准备工作
### adb工具
一般android sdk工具都自带了，目录一般为``/platform-tools/adb.exe``。也可以在这里：https://www.androiddevtools.cn/ 下载，才2M，很小。
### 手机
手机需要开启USB调试，且能用USB线和PC相连。是否连接完毕，可以使用``adb devices``命令查看
### Unity打包
需要勾选``Development Build`` ``Autoconnect Profiler`` ``Deep Profiling Support`` ``Script Debugging``几项。勾选了这几项后打包会很慢（不过一般的Profiler性能分析勾选``Development Build`` ``Autoconnect Profiler``就够了）。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201022161050.png)

（有些博文说Deep Profiling不支持IL2CPP，目前我这边测试的Unity版本是已经支持了的）

## 连接
- Step1

apk安装到手机后，使用命令把手机app连接到Unity的指定端口上，这样Profiler才能收到数据：

``adb forward tcp:55000 localabstract:Unity-包名``

如果55000端口不好使的话可以试试55001、55002等等

- Step2

打开Unity的Profiler窗口，在Enter Player IP中输入``127.0.0.1``即可

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201022161821.png)

Unity自己会自动搜索到之前配置的端口，并完成连接。

Unity的日志会输出``Attempting to connect to player IP: 127.0.0.1, ports 55000-55511, 4600``。这也表明端口限定为4600和[55000,55511]


## 注意事项
需要使用和apk一致的Unity版本来进行Profiler的连接

## 更多
Unity官方Profiler工具详细文档：https://docs.unity3d.com/Manual/Profiler.html

Unity官方性能分析工具UPR：https://upr.unity.cn/


## 补充
- Profiler死活连不上

1.关闭PC的防火墙

2.若是通过脚本自动打包的APK，则BuildOptions根据需要加上以下选项：BuildOptions.Development、BuildOptions.ConnectWithProfiler、BuildOptions.AllowDebugging、BuildOptions.EnableDeepProfilingSupport。

3.若依旧报错Failed to connect to player ip:127.0.0.1,port:34999，则在PlayerSetting里面勾选``Enable Internal Profiler*(Deprecated)``,笔者勾选这个后，上面都不需要设置adb forward命令，可以直接自动连接
