Clash 是一款优秀的V2ray/SSR 客户端，支持自动测速，自动选线,强大的自定义策略分流。支持网卡 Tun/Tap 全局模式的客户端软件。
同时支持 Windows/Mac/Linux 等系统，但是Windows支持最优秀。

下载地址:
https://github.com/Fndroid/clash_for_windows_pkg/releases


## step1
下载安装完成后，先去复制"Clash订阅码"（是个URL），拉取IGG服务器信息
## step2 
打开Clash 主界面，选择 Profiles, 粘贴从复制的Clash订阅码，点击 Download 按钮，下载配置文件。 clash默认的config.yaml 文件千万不要删除，删除了可能会导致clash无法正常运行!(笔者删除了发现并没有问题。。。)
## step3
右键点击下载好的XXX.yaml 配置 ， 选择 Settings 将订阅文件24小时（Update Interval填入24即可）自动更新一次。
## step4 
点击 General , 返回主页。 将 System Proxy 开启 即可打开代理。Clash 默认监听端口为 7890（可以在General->Port中修改端口）, 其他程序想要自定义设置代理，只需手工设置代理地址为: 127.0.0.1 端口:7890 即可。一个端口同时支持 HTTP/SOCKS5 协议。

如果要支持Git,IDE 等工具的全局代理功能，请参考Clash进阶教程：Clash For Windows Tun网卡模式

## 参考文档
https://igghelper.com/helper/?p=413