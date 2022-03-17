这个报错一般是相关的库没有引入，笔者查看详细报错信息提示的是libxlua.a未引入
返回XCode，确实未看到对libxlua.a的引用，如下图：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220317190945.png)

但是项目工程里面确确实实是有的，且放在Plugins/iOS（或者Plugins/../iOS）目录下的。

最后发现原因是Unity内未为其勾选目标平台
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220317191258.png)

如上图，勾选上iOS后，再导出的XCode工程就有对libxlua.a的引用了。