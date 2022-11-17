TextMeshPro已经收归Unity官方通过UPM（Unity Package Manager）进行统一管理了。目前(2022/11/16)默认情况下是引入到项目中（通过Packages/manifest.json文件进行引用管理），源码并不是直接集成到Assets目录下的，而是放置在``Library/PackageCache/com.unity.textmeshpro@版本号``这个目录下的。

若要集成代码，可以在UPM中删除TextMeshPro，然后把上述源码目录中的所有内容拷贝到Assets目录下即可。
