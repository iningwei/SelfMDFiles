老的渲染管线的shader，可以通过物体的material的``SetFloat()``,``SetTexture()``等函数来设置shader中的参数，从而达到需要的效果。
新的URP渲染管线，依旧是使用该方法进行设置，但是需要注意的是使用ShaderGraph编辑的.shadergraph文件变量参数的名字比较特别。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210428183208.png)
如上图是一个Float变量的面板，当对其使用``SetFloat()``函数时，传入的变量名需要是红色框选的Reference中的名字，而不是绿色框中的Name，这一点要注意了