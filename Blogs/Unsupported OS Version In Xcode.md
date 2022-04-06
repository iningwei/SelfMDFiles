通过XCode给同事iOS手机装包，报错Unsupported OS Version In Xcode。
原因：同事iOS手机刚升级了iOS 15.4,Xcode未配置对应版本的设备支持文件

解决方法：
- 下载对应版本的设备支持文件[iOS-DeviceSupport](https://github.com/iGhibli/iOS-DeviceSupport)
- 放到Xcode的对应目录下
Open the DeviceSupport folder at ``Applications/xcode.app/contents/Developer/platform/iPhoneOS.platform/DeviceSupport``
(Right click the Xcode app and select “Show Package Contents” to get into the xcode.app folder)