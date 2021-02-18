AraxisMerge功能强大，支持对文本、文件夹内文本的比较。


## 设置SVN的比较工具为AraxisMerge
SVN自带比较工具不太清爽，因此在安装了AraxisMerge后，可以设置为AraxisMerge。

- step1

打开SVN的Settings界面，选中Diff Viewer，设置如下：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210218190621.png)

注意使用External路径后面需要加上``%base %mine``，且注意和原始路径之间有个空格

- step2
Diff Viewer下的Merge Tool选项设置如下图：

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210218190837.png)

需要注意在路径后还要加上``%theirs %merged %mine``，以及空格