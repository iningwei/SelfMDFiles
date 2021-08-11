## 情况说明
据说OneDrive是Win10自带的，笔者机器，点击图标后无法打开，没有任何响应。
正常情况下在机器右下角有OneDrive的标示。

## 解决方法
注册表修改DisableFileSyncNGSC为0
```
1.按Windows+R键打开运行窗口。

2.输入regedit并回车打开注册表。

3.找到HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\OneDrive 路径。

4.点开DisableFileSyncNGSC文件并将其值设置成”0”。
```

笔者通过该方法顺利解决问题。若上述修改注册表后依旧无法解决问题，则考虑下述组策略的调整。
```
1. 按Windows+R键打开运行窗口。

2.输入gpedit.msc并回车打开组策略。

3.在组策略编辑器中，按照本地计算机策略>计算机配置>管理模版>Windows组件>OneDrive路径，找到Prevent the usage of OneDrive for file storage.并把它设置成“未设置”或者“禁用”状态。
```
## 其它解决方法汇总
下述方法是网上搜索到的最多的解决方法，但是笔者尝试后无效。
```
请您在键盘上点击 【Windows 按键 + R】 ；

请您在弹出的运行/ Run视窗输入：【%localappdata%\Microsoft\OneDrive\onedrive.exe /reset】；

然后请点击【OK】；

此刻OneDrive的图标会消失一至两分钟；

若OneDrive的图标仍未出现，请您在Run视窗输入:【 %localappdata%\Microsoft\OneDrive\onedrive.exe】
```