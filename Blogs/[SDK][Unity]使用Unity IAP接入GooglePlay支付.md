谷歌支付（In-app Billing）应用其实并不是直接和Google Play的服务器进行通讯，而是经过进程间通讯（IPC）向Google Play发送结算请求并接收Google Play返回的响应。因此要求设备必须配置好可用的Google Play应用。


笔者只以客户端流程和开发者后台内部测试下的注意事项进行说明，网络游戏中需要服务端和谷歌后台校验的也有一堆设置，具体请自己查询相关文档。
## 谷歌开发者后台准备工作
1，注册[谷歌开发者](https://play.google.com/console/developers)账户（需要交纳25美金，一次性的）
2，开发者后台创建项目
3，选择一种测试模式并上传aab应用文件
目前谷歌已经不支持apk格式包的上传，需要打包为aab格式并上传。
在发布正式版前，谷歌提供了三种测试模式，按照开放的尺度，依次为Internal testing（内部测试）、Closed tesing（封闭式测试）、Open testing（开放式测试）。
笔者选择了内部测试，因此在Internal testing页面提交了应用并完整填写了需要的其它信息（可测试人员邮箱、支持国家地区、隐私权、GooglePlay商店内展示信息）。需要注意这里的测试人员邮箱配置和应用内支付测试人员邮箱是不一样的概念。这里测试人员邮箱配置好后，可以通过开发者后台提供的链接，分享给对应测试人员，测试人员通过该链接点击接受测试邀请后才能激活测试。这样其才可以通过链接下载测试APP。

需要注意的是必须要对应用进行签名且需发布为release包才可以提交，否则会提交失败，若提交失败需要升级版本号，并重新提交。
4，配置应用内商品
谷歌支付支持的商品类型主要为"应用内商品"和"订阅"两种类型。
应用内商品主要需要设置产品ID，APP代码内部也是通过产品ID和产品唯一映射的。同时需要配置好产品名称，描述，并按照价格模版设置价格（不能任意设置价格，需要使用谷歌内置的价格模版）。

## Unity工作
- 安装IAP插件
1，Window->Package Manager打开包管理界面
2，搜索In App Purchasing，下载（注意需要在包管理界面中选择过滤条件为``Packages:Unity Registry``,否则搜索不到），下载后的包体内容并不存在于Assets目录下，而是在Project视图下和Assets同级别的Packages目录下。
另外包管理界面还提供IAP的Demo下载（代码中有对非消耗品的恢复处理相关代码）。

- 配置Services界面
Window->General->Services打开服务界面，启用Analytics并配置In-App Purchasing的Options项下的Key。
配置后有可能会提示错误，可以通过点击左上角的Dashboard打开Unity Dashboard后台网页后刷新一下即不再提示错误。
- 配置Receipt Validation Obfuscator
Window->Unity IAP->Receipt Validation Obfuscator,打开界面后，按照提示，在谷歌开发者后台的 创收（Monetize）->创收设置（Monetization setup） 界面中的Licensing中找到Key，并拷贝到界面中(需要注意的是上面提到的Services界面中In-App Purchasing选项的Options中配置的也是这个Key)。然后点击Obfuscate Google Play License Key按钮，会在项目工程中自动生成需要的GooglePlayTangle.cs文件（同时也会生成AppStore支付需要的AppleTangle.cs文件）
- 后续就是支付代码集成相关工作了
## 测试
对于内部测试模式下的应用，提交到后台后状态变为``已面向内部测试人员发布``即可进行测试，并不需要审核完毕。且应用包也不需要是从商店下载的，本地打的包亦可（需要注意的是包签名，包名必须和后台一样，笔者通过测试发现包版本号和后台的不一致亦可支付成功）。

官方文档[测试您的 Google Play 结算库集成](https://developer.android.google.cn/google/play/billing/test#test-purchases-sandbox)提供了覆盖测试的注意事项，测试人员需要多加研读。

### 沙盒测试
在开发者后台的：所有应用->设置（Setup）->许可测试（License testing）界面中可以配置支付测试的测试人员邮箱。这些人员在支付时就可以不用真实扣费了，且没有绑卡的需求。

### 正式环境支付
正式版本下的支付，需要用户绑定支持国际支付的信用卡或者银联卡。

## 其它
也可以不通过unity提供的iap进行谷歌支付接入，通过使用谷歌官方出的unitypackage包也可以接入，具体可以参考[在 Unity 项目中使用 Google Play 结算库](https://developer.android.com/google/play/billing/unity?hl=zh-cn)

## 一些值得看的文档
1，[App Store与Google Play平台测试轨比较](https://www.jianshu.com/p/f340f652b425)

2，[设置开放式测试、封闭式测试或内部测试](https://support.google.com/googleplay/android-developer/answer/9845334?visit_id=637425645116399940-578461352&rd=1)