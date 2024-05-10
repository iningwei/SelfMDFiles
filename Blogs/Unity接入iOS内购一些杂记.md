接入使用的是Unity官方的IAP包。笔者之前有写过接入文档：https://blog.csdn.net/iningwei/article/details/107319860

这次接入遇到了一下一些问题，这里总结记录一下.


## 1，Apple Store Connect 商务界面地址验证失败，更新法律实体地址
![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/05/address%20fail.png?raw=true)

我们按照公司营业执照，翻译为英文，填了后台地址。但是一直显示地址验证失败。 我们联系了苹果商务折腾了好几天也没给我们解决。

偶尔在知乎上看到这个：

![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/05/address%20fail2.png?raw=true)

试了下，重新精简了下翻译，MD竟然通过了。

我们自己理解的是，Apple Store后台有一套自己认定的地址格式，如果你的不满足，那就无法通过。所以这一步填写，不必太拘泥翻译，多试试出奇迹。

这一步通过后，还需要继续签署付费应用程序协议、添加银行账户。审核通过后才能进行支付测试，否则是无法测试的。

[App Store Connect 添加新银行账户流程说明](https://blog.csdn.net/weixin_43113929/article/details/130879943)

## 2，沙盒测试，SDK已弹出购买成功提示，但代码层，报错：InvalidSignatureException:Exception
笔者环境如下：
```c
Unity 2021.3.8f1
iphone11,os 17.4.1
iap version:4.3.0(com.unity.purchasing:"4.3.0") （这个是PackageManager安装时，默认安装的版本）
```
报错详情为：
```csharp
InvalidSignatureException:Exception of type `UnityEngine.Purchasing.Security.InvalidSignatureException` was thrown. at UnityEngine.Purchasing.Security.CrossPlatformValidator.Validate(System.String unityIAPReceipt)[0x00000] in <0000000000000>
```

在论坛:[InvalidSignatureException thrown during receipt validation on iOS 16.6](https://forum.unity.com/threads/invalidsignatureexception-thrown-during-receipt-validation-on-ios-16-6.1468313/)，找到了一些讨论。

总结下来两种方式:
- 1,是升级IAP包
- 2,沙盒环境也使用AppleTangle进行验证，而不是AppleStoreKitTestTangle

由于笔者满足2，于是升级了IAP包到4.11.0，再测试，错误消失！

## 3，客户端把返回的receipt发到服务端，服务端往apple store验证失败，报错21002
这是服务端那边的错误，一般而言验证receipt报错21002是格式的问题。

参考：
[IAP 内购二次验证（出现的问题21002及解决方案）](https://juejin.cn/post/6844903781499420685)
[Error 21002 when validating receipt](https://forums.developer.apple.com/forums/thread/706443)