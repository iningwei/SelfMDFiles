XCode11已不再自带Application Loader了。可以通过以下方式进行ipa的上传。
- XCode构建ipa时直接上传
在Archive出文件后，在Archive界面（Window->Organize）通过DistributeApp构建能上传AppStore的包时，在引导界面有一个选项是可以直接上传到Store的。
这种方式在遇到卡顿，报错时不太好处理，又要重新打包构建，很耗时间

- 使用Transporter可视化APP
在mac中下载该免费可视化上传工具，通过XCode构建出ipa，拖到该工具界面中即可上传。
笔者亲测个人账号注册的开发者也可使用该工具上传，且无需VPN。

- 使用altool
可以使用 xcrun（包含在 Xcode 中）来调用 altool，该命令行工具用于公证、验证并上传您 App 的二进制文件至 App Store。在“终端”的命令行中指定以下命令之一：
```c
$ xcrun altool --validate-app -f file -t platform -u username [-p password] [--output-format xml]
$ xcrun altool --upload-app -f file -t platform -u username [-p password] [—output-format xml]
```


另外如果没有mac机器，在windows系统上可以考虑使用:[121xuexi](https://app.121xuexi.com/)(没用过，安全责任自己负担)
 

参考文档：[谈一谈 IPA 上传到 App Store Connect 的几种方法](https://www.jianshu.com/p/7b76466e4bed)

## 补充
1，上传完成后appstoreconnect界面不显示上传的包
一般需要等待个半个小时左右，估计上传后苹果还有一道审核机制，无论成功还是失败都会给你发送邮件的，关注邮件即可。
成功后的邮件内容如：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220609111634.png)
2，appstoreconnect如何构建 
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220609111741.png)
如图在包体上传成功后这里会有个+号，点击后即可选择目标版本进行构建