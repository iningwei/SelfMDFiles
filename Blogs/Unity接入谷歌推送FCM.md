# 前言

自2014年Firebase被Google收购以来，其已成为Google移动平台的主要产品。

FCM已完全替代谷歌自己的GCM作为安卓手机的首要推送服务（当然这只针对海外，国内厂商机器默认是没有安装谷歌服务的，且大部分手机完全不支持安装）。



## 接入流程

官方文档：[使用 Untiy 设置 Firebase Cloud Messaging 客户端应用](https://firebase.google.com/docs/cloud-messaging/unity/client?authuser=0)已经有了很详尽的客户端接入流程，包括Android和iOS。

## 一些注意项

笔者在接入Android过程中遇到以下一些问题需要特别拎出来说明以下：

- FirebaseMessaging.TokenReceive事件回调中获得Firebase分配给你的FCM token，用户可以把该token透传到自己的服务端，以便做差异化推送。且需要注意的是只有FCM第一次初始化成功才会收到TokenReceive的回调，后续启动手机不再获得，所以客户端用户需要自己处理token缓存。另外若用户卸载重新安装App，那么会再次获得一个新的token。
- FirebaseMessaging.MessageReceived事件回调用于处理推送消息

若是App在后台收到推送，通过点击通知栏激活App回前台，那么此时e.Message.Notification为null。若在App运行期间收到推送，那么不会在通知栏展示，这个时候回调中的e.Message.Notification不为null，用户可以通过e.Message.Notification.Title和e.Message.Notification.Body获得通知的标题和内容，进而决定是否在游戏内通过其它方式进行展示。

另外无论是后台收到推送还是运行期收到推送，经过笔者测试e.Message.Data都包含了消息的参数数据。

另外笔者有看到过一个外国友人，通过在Unity的OnApplicationPause(bool appPaused)系统函数，当appPaused为false的时候来获得FCM透传的参数信息（猜测主要用于处理App进程杀了的情况下收到push，点击通知栏的跳转处理），相关代码如下：

```c#
AndroidJavaClass unityPlayer=new AndroidJavaClass("com.unity3d.player.UnityPlayer");
AndroidJavaObject curActivity=unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
AndroidJavaObject curIntent=curActivity.Call<AndroidJavaObject>("getIntent");
string value1=curIntent.Call<string>("getStringExtra","key1")
```



## 如何测试

可以通过Firebase的后台提供的可视化界面进行测试。包括对所有目标包名的应用推送和对指定token的用户推送。

也可以通过服务端集成的后台服务进行推送。

还可以使用Curl或者PostMan发送HTTP POST请求进行推送：https://www.jianshu.com/p/73d29b77b37c  https://www.jianshu.com/p/6cf4dd76e508。

需要注意的是在国内push的达到率比较低，建议如下操作：

1，若推送多次接受不到，那么卸载重装，重新获得一个token

2，不要太频繁发送推送进行测试，也不要发送同样的推送内容。这样可能会被FCM服务器过滤掉。

3，用Firebase FCM后台进行推送测试成功率太低了，也看不到服务端是否发送成功。建议使用服务端集成或者PostMan的方式。

4，国内安装了谷歌服务的手机能否在杀掉进程的情况下收到推送消息?
笔者通过对数量不多的国内和国外手机进行测试，发现：国内手机收不到（搜集了一些资料，获得的信息是大部分收不到）；海外Pixel手机顺利收到。




## 补充
有个unity fcm的小demo：https://github.com/firebase/quickstart-unity/tree/master/messaging/testapp

