## 详情
Excel在没有关闭的情况下，就更新了SVN，导致更新失败，提示进行Clean Up。但是Clean Up报错：Failed to run the WC DB work queue associated with xxxx


## 解决办法
工程都有.svn文件夹（默认是隐藏的），打开后，确实能看到wc.db文件。

下载[该工具](https://download.csdn.net/download/iningwei/14125670)，把``sqlite3.exe``和``清理SVN-WCDB报错.bat``这两个文件放到.svn同级目录下。双击bat，完成处理。然后再Clean Up就不会报错了


- 补充

bat命令只包含：``sqlite3 .svn/wc.db "delete from work_queue"``

没有包含：``sqlite3 .svn/wc.db "select * from work_queue"``,因为该条命令指示用来显示数据的。根据自己的需要可以加上该句。