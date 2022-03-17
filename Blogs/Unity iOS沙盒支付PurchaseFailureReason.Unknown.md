使用Unity的IAP插件集成了iOS支付。笔者昨天沙盒支付还OK，今天（2020年10月15日）准备出Dis包了，尼玛支付不了了。日志显示的是``PurchaseFailureReason.Unknown``。

商品配置、协议和税务都又从头查了一遍，没有明显问题。无法确定缘由，初步怀疑是Apple服务器的问题（不过苹果的 [SystemStatus](Https://developer.apple.com/system-status/) 系统显示App Store Sandbox是正常的）。

相关文档：

[Test in-app purchase inconsistent on iOS devices](https://forum.unity.com/threads/test-in-app-purchase-inconsistent-on-ios-devices.917348/)

[Unity Codeless IAP, iOS, Sandbox, PurchaseFailureReason.Unknown](https://answers.unity.com/questions/1333653/unity-codeless-iap-ios-sandbox-purchasefailurereas-1.html)

## 2020年10月22日补充
果然是苹果服务器的问题，今天上午测试OK了。

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201022153701.png)

从截图中可以看到苹果说从2020年10月16日上午12点到2020年10月22日上午8点，沙盒支付出现故障。

吐槽一下：

a.时间不对，我可是15号就遇到问题了

b.这期间沙盒出现问题，也不及时在SystemStatus上更新状态（依旧显示的是正常状态），等修好了，给你来个提示。窝巢，浪费劳资N久时间。