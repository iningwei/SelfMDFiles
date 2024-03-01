笔者Win10系统，突然有天发现项目的状态图标都不显示了。
## 原因
其它文件图标优先级更高覆盖了SVN的状态图标了
## 解决方法
- step1 
注册表找到``计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers``
会看到Tortoise1Normal、Tortoise2Modified等SVN相关文件夹，且此时它们在ShellIconOverlayIdentifiers目录下是靠后的。笔者通过重命名方式，在这些文件夹前加了很多空格，强制使它们排在前面即可。
- step2
win+r，输入explorer.exe,打开windows资源管理器。然后在任务管理器中,结束资源管理器任务。再通过explorer.exe重启资源管理器，上述注册表的修改即可生效。