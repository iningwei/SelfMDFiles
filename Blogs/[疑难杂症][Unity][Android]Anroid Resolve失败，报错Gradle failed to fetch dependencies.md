同事空工程导入FB广告SDK后，安卓依赖拉不下来。
报错信息有：
```java
FAILURE: Build failed with an exception.

* What went wrong:
A problem occurred configuring root project 'PlayServicesResolverGradle'.
> Could not resolve all artifacts for configuration ':classpath'.
   > Could not resolve com.android.tools.build.jetifier:jetifier-processor:1.0.+.
```
Gradle这一块我们都是使用Gradle Installed with Unity这个选项。同样的工程，我这边可以拉下依赖。

把我这边的SDK发给同事，依然拉不下来。



最后没辙了，我觉得有可能是他本地的Gradle有问题。于是喊他把Gradle目录(%user%/.gradle)删除，重新拉(Android Resolve的时候会自动把Gradle安装到本地)。果然，顺利解决。