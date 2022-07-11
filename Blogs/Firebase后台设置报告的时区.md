Firebase服务端是使用的UTC世界时，我们打开Firebase后台会看到各种以时间为纬度的统计分析图表。
一般App自己也会有自己的后台按照某个时区进行统计，二者调整为一个时区才能更好对比。

Firebase后台时区设置方法隐藏的比较深：
1，首先打开Firebase Analytics的详细页（不是Dashboard页，而是在Dashboard页面内点击“在Google Analytics中查看更多”按钮后打开的页面）
2，点击页面左下角的设置图标按钮
3，打开的页面中，点击媒体资源设置，即可看到时区的设置。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220708165337.png)