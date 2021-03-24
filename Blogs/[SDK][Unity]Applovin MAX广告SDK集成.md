## 说明
github上有[AppLovin-MAX-Unity-Plugin](https://github.com/AppLovin/AppLovin-MAX-Unity-Plugin)，实际上是个Demo工程，并不是干净的unityPackage包。

## 集成
Applovin的文档和集成需要的资源都需要先注册账号，然后才能下载

注册后，在[这里](https://dash.applovin.com/documentation/mediation/unity/getting-started/integration)可以下载最新的Unity Plugin。



各个Mediated Networks查询：[地址](https://dash.applovin.com/documentation/mediation/unity/mediation-adapters)


## 注意事项
- 需要注意的是android需要开启Jetifier，文档中也提到了可以通过Android Resolver开启:Assets > External Dependency Manager > Android Resolver > Settings > Use Jetifier.

- AdColony,Facebook,Amazon广告需要配置network_security_config，官方文档中都有提到。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210317164856.png)
如图，通过勾选AdColony，可以获得更详细的配置信息