## 前言

自2014年Firebase被Google收购以来，其已成为Google移动平台的主要产品。

FCM已完全替代谷歌自己的GCM作为安卓手机的首要推送服务（当然这只针对海外，国内厂商机器默认是没有安装谷歌服务的，且大部分手机完全不支持安装）。

## 海外Push通道
push整个推送环节中，push通道是决定整个发送率和到达率的关键一步，push通道的稳定会影响整个push推送的质量。

海外push通道类型主要分为：FCM通道（由 Google 提供的推送服务）、厂商通道和长连接通道，三个通道各自有各自的特点，下面进行详细介绍

- FCM通道：即Firebase 云消息传递，是由Google 提供的推送服务
由于海外手机大都具有google服务，所以fcm通道是一个优先选择的通道，可以提升整个push推送的到达率。fcm通道发送到用户手机上主要有两种方式，一种是通知，一种是透传。通知的特点是发给用户通过手机系统发到用户设备上，不会拉起app进程，app进程不在一般也能收到，所以push消息的到达率较高，但是通知不能自定义样式，只能使用系统的默认的样式。透传的特点是发送时fcm通道拉起app Push进程，然后将对应的消息透传给客户端，客户端根据对应的消息类型进行展示，透传的好处是客户端完全可以自定义push的样式，增强push的点击率

- 厂商通道：厂商通道主要是包括小米、华为、oppo等手机厂商通道
手机终端厂商推出的推送服务，通过接入厂商SDK，内部服务端可以将消息推送到手机系统的服务端,再下发至客户端内部的厂商SDK，由操作系统进行相应展示，点击后唤起相应APP，这样可以避免APP进程被杀死后消息无法触达用户。但是在海外手机厂商通道没有国内做的完善，一般稳定性不是很高，到达率相对较低，且目前手机厂商通道的消息只能走系统通知，不能走透传自定义push样式，另外海外华为push通道没有回执数据，导致无法统计整个链路完整的数据。但是厂商通道在海外是对fcm通道的一个补充，当fcm通道不稳定和消息丢失后，或者部分手机没有google服务即没有fcm通道时，可以采取厂商通道进行补充发送，提升整体的push到达率

- 长连接通道
是建立用户设备与服务端的一条链路，可以进行消息数据推送，通过长连接也可以进行APP状态监控，所以长链接推送一定要保证app长连接没有断开，即app进程活着。长连接推送的好处是app消息的到达率较高，一般可以达到95%以上，但是坏处是长连接断掉，整个通道都不可用，所以需要业务团队需要投入大量的研发资源去避免app的长连接进程不被操作系统杀死，进行app进程的保活。

更多内容请参考：[海外产品push搭建全流程](https://zhuanlan.zhihu.com/p/315501207)
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

## 国内安装了谷歌服务的手机能否在杀掉进程的情况下收到推送消息?

笔者通过对数量不多的国内和国外手机进行测试，发现：国内手机收不到（搜集了一些资料，获得的信息是大部分收不到）；海外Pixel手机顺利收到。




## 补充
有个unity fcm的小demo：https://github.com/firebase/quickstart-unity/tree/master/messaging/testapp

