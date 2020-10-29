Unity Stencil Buffer入门


## 一个典型案例引发的血案

在很多功能场景的应用中都能见到Stencil的身影。模板缓冲并不是一个新技术（已经出现10多年了），在Unity中从4.2 Pro和4.6 Free开始支持。

模板缓冲的允许你为屏幕上的每个像素额外存储一个无符号的8位整数（0-255）。这些值可以被后来的shader进行查询和比较，用来决定最终像素的显示情况。

把如下代码加入到``shader A``中，可以把输出到屏幕的每个像素的模板缓冲值设置为1：
```c
// Write the value 1 to the stencil buffer
	Stencil
	{
		Ref 1
		Comp Always
		Pass Replace
	}
```

把如下代码继续添加到``shader A``中，让其具有mask的功能，它本身不会往屏幕绘制任何东西，只是通过设置模板mask来控制其它shader绘制的像素：
```c
Tags { "Queue" = "Geometry-1" }  // Write to the stencil buffer before drawing any geometry to the screen
    ColorMask 0 // Don't write to any colour channels
    ZWrite Off // Don't write to the Depth buffer
```

到目前为止，``shader A``不会有任何可视化的输出，它所做的只是把已经渲染的像素的模板缓冲值设置为1。我们可以利用模板缓冲对其它shader的输出进行遮罩(mask)处理。比如我们对``shader B``加入如下代码：
```c
// Only render pixels whose value in the stencil buffer equals 1.
    Stencil {
      Ref 1
      Comp Equal
    }
```

shader A和shader B结合使用的效果可以用下图来说明：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201014164430.png)

- 相框包含一个不可见的长方形区域，该区域使用了shader A，由于其Queue为Geometry-1，先渲染，因此把该区域对应的像素的模板缓冲的值设置为了1，且其不输出任何可视化的信息
- 角色使用了shader B,它进行了模板缓冲测试，只有模板缓冲值为1的区域可以被渲染


[原文：Using the Stencil Buffer in Unity Free](https://alastaira.wordpress.com/2014/12/27/using-the-stencil-buffer-in-unity-free/?utm_source=tuicool&utm_medium=referral)

[官方文档：ShaderLab: Stencil](https://docs.unity3d.com/Manual/SL-Stencil.html)


## 更多补充说明
### 



开发工具相关
Android
iOS
C#
Js&Ts&Nodejs
Unity
Shader
Python
SDK
