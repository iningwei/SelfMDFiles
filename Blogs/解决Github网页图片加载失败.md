## 情况说明
最近打开Github经常会遇到用户头像或者仓库中的图片无法预览。F12打开控制台也能看到一堆报错信息。

## 解决方法
- 找到hosts文件

Win: ``C:\Windows\System32\drivers\etc\hosts``

Mac: command+shift+g打开``前往``输入``/etc/hosts``

- 把下列内容粘贴到hosts中
```c
# GitHub Start 
140.82.113.3    gist.github.com
185.199.108.153    assets-cdn.github.com
199.232.96.133    raw.githubusercontent.com
199.232.96.133    avatars0.githubusercontent.com
199.232.96.133    avatars1.githubusercontent.com
199.232.96.133    avatars2.githubusercontent.com
199.232.96.133    avatars3.githubusercontent.com
199.232.96.133    avatars4.githubusercontent.com
199.232.96.133    avatars5.githubusercontent.com
199.232.96.133    avatars6.githubusercontent.com
199.232.96.133    avatars7.githubusercontent.com
199.232.96.133    avatars8.githubusercontent.com
# GitHub End
```

## 原因分析
猜测是域名解析错误（DNS污染、hosts设置错误、DNS缓存未更新等）。因此使用hosts配置，域名解析直接指向IP地址来绕过DNS的解析

## 更进一步
由于IP地址经常变动，因此上述提供的IP地址不一定能用，可以在[ipaddress](https://www.ipaddress.com/)输入域名后来获得最新的IP，比如输入：avatars0.githubusercontent.com，获得的IP为199.232.96.133：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210105183345.png)