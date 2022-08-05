unity中定义了5个渲染队列：

1.Background，对应索引号1000，该队列最先被渲染

2.Geometry，对应索引号2000，默认的渲染队列，大多数物体都使用该队列，不透明物体使用该队列

3.AlphaTest，对应索引号2450，需要透明度测试的使用该队列

4.Transparent，对应索引号3000，需要透明度混合的使用该队列

5.Overlay，对应索引号4000，该队列最后被渲染

索引号越小，则越早被渲染。可以看到，背景最早被渲染，然后是不透明物体，再然后是透明物体

skybox渲染顺序为2500.5


//TODO：more detail blog
参考文档：
[Unity 中渲染顺序的理解以及一些坑点 以及2.5D游戏中的渲染排序解决方案](https://blog.csdn.net/yinfourever/article/details/106073131)
[Unity Shader-渲染队列，ZTest，ZWrite，Early-Z](https://www.cnblogs.com/heweiwei/p/11174322.html)