国内购买了Google手机后，激活是个挺麻烦的事情，主要是因为激活需要连接Google邮箱。对于路由器没有搭建梯子的WIFI网络来说要实现激活就比较麻烦了。
以下一些我们能轻易想起来的方案，都不可用，比如：
- 1，	使用A手机开启梯子，分享热点，B手机（待激活的Google手机）连接热点
- 2，	PC电脑挂上梯子，使用WIFI助手分享网络，B手机连接热点
- 3，	笔记本挂上梯子，使用笔记本自带热点分享工具分享WIFI网络，B手机连接热点

若在只有梯子的情况下，使得分享的热点自带梯子，该如何操作呢？且看下述方案：

1，	下载ConnetifyHotspot，安装。

直接下载官方最新版，不要下载老的破解版（有bug）。官方版本够用了。

该软件的作用是能共享适配器的网络为Wifi热点，供外部机器连接。因此下一步我们就要想办法制造一个带梯子的网络适配器。

我们笔记本连接的网络适配器是不带梯子的，笔记本开了梯子软件，这些网络适配器也不带梯子。这些前提要记住。

2，	带梯子的网络适配器
网上有推荐使用SSTap，但是笔者测试后发现该软件已经停止维护了，且极度不稳定


推荐使用Netch，可以配置Shadowsocks等多种类型服务器。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/xx-20211024-1.png)

模式选择Bypass LAN and China

配置好并启动后，在电脑的``控制面板->网络和Internet->网络连接``面板内即可看到Netch创建的适配器。

但是无奈的是Netch创建的适配器，在ConnetifyHotspot面板内却不显示。
 
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/xx-20211024-2.png)
即上图中下拉列表中无对应Netch的适配器。

后来笔者找到了个Speedify软件，用该软件对Netch的适配器加速，然后在Connectify中选择Speedify作为目标适配器。然后启动Connectify的热点，使用待激活的B手机连上后顺利激活手机。

备注：Speedify是付费软件，但是有2G免费流量，对激活手机来说够用了。

