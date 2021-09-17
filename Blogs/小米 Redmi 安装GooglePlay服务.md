雷总良心，mi系列手机都可以愉快玩耍谷歌全家桶。笔者以Redmi为例讲一下安装流程。

## 手机信息
机型：Redmi 9A (红米低端机，当前市价约600)
MIUI版本：12.0.13 | 稳定版
安卓版本：10

## 需要下载的软件包
Google Services Framework
Google Play services
Google Play Store

## 下载
[apkmirror](www.apkmirror.com)(自备梯子)
打开上述网站，输入关键词查找，即可下载。
需要注意的是下载时要选择正确的CPU架构和安卓版本。
查看机器的CPU架构，可以使用``adb shell getprop ro.product.cpu.abi``,笔者使用命令后获得我的红米9A机器的CPU架构信息为``armeabi-v7a``

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210810122620.png)
上图为依次下载的软件。

如果您手机CPU架构也是armeabi-v7a，安卓版本和我的差异不大，那么可以直接下砸：[redme 9a gp-software](https://download.csdn.net/download/iningwei/23357287)

## 其它
把上述软件依次安装到手机，安装后在设置中修改各软件为自启动。