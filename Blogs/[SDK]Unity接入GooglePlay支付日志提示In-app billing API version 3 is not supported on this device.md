使用Unity的IAP接入了GooglePlay支付，之前通过测试帐号测试顺利通过（测试机为国内某米手机和国外pixel）。过了一段时间由于需要更改包名，于是把相关key什么的重新处理了一下，再在pixel手机上测试时就无法顺利支付，通过日志发现IAP初始化并没有成功，发现一条日志信息是：In-app billing API version 3 is not supported on this device.
而且发现pixel手机在其它游戏中也无法支付，由于其它游戏做了统一的提示处理（比如Google Play Service is not available之类的）,故也看不到具体原因。

一开始分析可能是Pixel手机的GP服务坏了。后来才发现原因是GP登录了多个帐号，且测试时使用的帐号为开发者帐号。
于是移除GP上的开发者帐号和其它帐号，只登录了一个普通的帐号（该帐号已加入我自己APP的测试名单和支付测试名单内），发现自己的APP支付初始化顺利完成，同时在其它APP中也可以弹出支付界面了。

其它建议：
1，国内确保有梯子，且网络正常
2，打开Google Play App在付费栏下载付费App，看是否可以弹出支付窗口，若能弹出说明GP服务没有问题
3，打开其它App，看能否弹出购买窗口
4，设置界面把Google Play Service和Google Play Store的缓存数据清理一下，然后重启手机
