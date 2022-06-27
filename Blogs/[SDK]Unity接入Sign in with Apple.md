iOS13之后引入了``Sign in with Apple``，Sign In with Apple是跨平台的，可以支持iOS、macOS、watchOS、tvOS、JS。对iOS上的App而言，若引入第三方或社交登录（Facebook,支付宝等）那么则必须接入SIWA（Sign in with Apple）。
满足以下条件可以不使用苹果账号登陆：
1，应用仅使用开发者公司自己的账号设置和登录系统；
2，应用归类为教育、企业或商业应用，要求用户使用现有的教育或企业账号登录；
3，应用使用政府或行业支持的公民身份识别系统或电子D对用户进行身份验证；
4，应用是特定第三方服务的客户端，用户需要直接登录其电子邮件、社交媒体或其他第三方账号才能访问内容。
详见苹果商店的审核条款[App Store Review Guidelines - Apple Developer](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)

## 配置要求
苹果系统10.15+；手机或平板系统13.0+；XCode11+
## 证书配置
需要在启用Sign in with Apple的Apple ID中勾选Sign in with Apple。（若一开始证书未配置SIWA，那么需要勾选后重新下载Profiles，并双击导入XCode）
## 图标要求
按钮图标必须按照苹果要求的样式，可以参考使用这个在线图标生成器：https://appleid.apple.com/signinwithapple/button
## Unity接入
- 官方插件
之前Unity Technologies有提供Sign in with Apple的插件，在AssetStore搜索关键字即可获得。但是在更新到1.1.1版本后就下线了（笔者这里提供个下载地址：https://download.csdn.net/download/iningwei/85800794）。
笔者使用该版本接入过两次，第一次很顺利没有任何问题，第二次接入在我们游戏中弹出授权页面后点取消会报``libc++abi: terminating with uncaught exception of type Il2CppExceptionWrapper``错误，造成程序卡死，目前未定位到具体原因（但是笔者单独测试SDK是没有问题的，由于时间关系加上自己对iOS开发不熟<其实就是菜>就未深究，于是换了一个插件）
- Sign in with Apple Unity Plugin
这是个人开发的插件，在AssetStore即可下载，且提供有git下载：https://github.com/lupidan/apple-signin-unity
文档详细，接入稍微比上述的官方插件复杂一点点。推荐使用该插件，毕竟官方的已经下线，肯定是有它的理由的。

## XCode设置
1，Capabilities引入Sign In with Apple
2，增加 AuthenticationServices.framework 库，如果App最低使用版本低于13，需要为该库设置状态为``Optional``
参见上述Sign in with Apple Unity Plugin的git，有详细说明

## 测试
用户调用登录接口，第一次弹出授权页面会有用户名（可编辑），邮箱（用户也可以不允许使用真实邮箱，那样的话会随机生成一个邮箱，并发送到你AppleID的对应邮箱中）选项。用户授权AppleID登录该App后，再次调用登录接口弹出页不再显示用户名、邮箱信息（且接口中返回的数据也不包括name,email信息）。
### 取消授权
在iOS设备中可以取消对某个App的授权，具体操作为：``设置->AppleID->密码安全性->使用Apple ID的App``



## 其它
在App开发中，由于系统版本低于13的不支持SIWA，需要根据用户手机系统版本给予区分展示，即低于13的不显示Sign in with Apple按钮即可。

## 参考网址
[Sign in with Apple 登录详解](https://ihtcboy.com/2019/09/16/2019-09-16_Sign-in-with-Apple/)

 
 
