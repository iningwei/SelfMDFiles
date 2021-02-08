## Unity先导出XCode工程

## 增加本地化语言选项
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210208171121.png)

## 设置本地化语言
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210208171229.png)

在Supporting Files/InfoPlist.strings下为各个本地化语言设置文档，如上图输入``CFBundleDisplayName="要显示的名字";``

如果有需求在iPhone和iPad上也显示不同的名字，那么需要同时填入两个display name:
```
CFBundleDisplayName~iphone="要显示的名字1";
CFBundleDisplayName~ipad="要显示的名字2";
```

## Info.plist设置对本地化display name的支持
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210208171425.png)

增加Application has localized display name项，并设置值为YES

## 异常处理
上述设置完毕后，出的包并没有显示中文，原因是在InfoPlist.strings中Target Membership选项下没有勾选上本项目

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210208172013.png)
