报错信息为:``DllNotFoundException: Unable to load DLL 'FirebaseCppApp-6_15_0': The specified module could not be found.``

我这边项目SDK接入了很多，直接导入Firebase的相关SDK后，发现AndroidResolve拉下来的FirebaseSDK涉及到的aar(jar)因为版本有问题，打包后报错。

使用Unity空工程导入SDK，Resolve后，接入示例代码一切顺利。遂把空工程中拉下来的依赖库导入到项目中。

我这边一款很老的手机和夜神模拟器运行无误，无奈同事手机报错。

## 原因
空工程默认情况下只勾选了ARMv7平台，因此拉下来的依赖库也有平台限制。导致了同事ARM64的手机运行报错

## 解决办法
空工程中勾选ARMv7和ARM64,重新拉取依赖，再迁移依赖库到项目工程。顺利解决问题