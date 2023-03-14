Windows下 如何加速IDE,命令行的Git 拉取等问题，本教程介绍使用Clash 进行网卡级别加速的使用方法。
```
此模式可能会导致流量被计算进入客户端流量，所以非必须请勿长期使用此模式。
```

Clash 官方有简单教程,参考: https://docs.cfw.lbyczf.com/contents/tun.html#windows

## step1
打开Clash 主界面，选择 General – Service Mode – Manage – Install

在弹出的界面中选择 Install, 会弹出一个系统授权界面，点击确认。 安装完成后 Clash 会自动重启。

重启完成后，继续点开Clash 主界面，Service Mode旁边的地球变绿。代表安装成功。

## step2
开启Tun 模式 , 在 General 页面开启TUN Mode和Mixin这两个选项


## step3
在命令行窗口ping www.google.com，若能ping通，说明网卡级加速成功。如果使用完成，只需要把 Tun 与Mixin 模式的开关关闭，就可以使用常规模式了。

## step4
如果windows 系统开启TUN模式，在命令行或者其他终端还无法正常工作，请点击General->TUN Mode旁边的锯齿，把``Auto Detect Interface``关闭再试试看


## 参考文档
https://igghelper.com/helper/?p=402