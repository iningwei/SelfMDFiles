使用Unity的IAP插件集成了iOS支付。笔者昨天沙盒支付还OK，今天（2020年10月15日）准备出Dis包了，尼玛支付不了了。日志显示的是``PurchaseFailureReason.Unknown``。

商品配置、协议和税务都又从头查了一遍，没有明显问题。无法确定缘由，初步怀疑是Apple服务器的问题（不过苹果的[SystemStatus](Https://developer.apple.com/system-status/)系统显示App Store Sandbox是正常的）。

相关文档：

[Test in-app purchase inconsistent on iOS devices](https://forum.unity.com/threads/test-in-app-purchase-inconsistent-on-ios-devices.917348/)

[Unity Codeless IAP, iOS, Sandbox, PurchaseFailureReason.Unknown](https://answers.unity.com/questions/1333653/unity-codeless-iap-ios-sandbox-purchasefailurereas-1.html)

## 2020年10月22日补充
果然是苹果服务器的问题，今天上午测试OK了。

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201022153701.png)

