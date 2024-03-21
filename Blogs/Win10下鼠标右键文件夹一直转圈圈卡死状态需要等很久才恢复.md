## 原因
盗版软件污染了鼠标右键
## 解决方法
找到鼠标右键的污染源
- 下载ShellExView

[shexview官网](http://www.nirsoft.net/utils/shexview.html) or 
[shexview-x64](http://www.nirsoft.net/utils/shexview-x64.zip)

- 运行shexview.exe

点击 ``点击Options→Filter By Extension Type→Context Menu``

找到影响到鼠标右键的程序（会粉色显示）

- 依次关闭粉色程序，再点选 Options => Restart Explorer。挨个测试是否还会出现卡死情况
- 确定了是哪个程序后，就可以关闭该程序的右键菜单，解决问题