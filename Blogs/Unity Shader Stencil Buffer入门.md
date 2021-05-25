Unity Stencil Buffer入门


## 简介

在很多功能场景的应用中都能见到Stencil的身影。模板缓冲并不是一个新技术（已经出现10多年了），在Unity中从4.2 Pro和4.6 Free开始支持。

模板缓冲的允许你为屏幕上的每个像素额外存储一个无符号的8位整数（0-255）。这些值可以被后来的shader进行查询和比较，用来决定最终像素的显示情况。

## 模板测试
- 如果模板测试不通过，该像素会被丢弃
- 模板缓冲区中每个像素对应存放一个8位整数(0-255)
- 模板测试也是一个比较值的过程，将参考值和模板缓冲中的值进行比较
- 即使模板测试不通过，依然可以修改模板缓冲中的值。这一点和深度测试不一样。

### 语法
- Ref referenceValue

设置参考值，referenceValue(范围0-255)
- ReadMask readMask

参考值和模板缓冲的值，会和readMask进行按位与(&)操作，readMask(范围0-255)，默认值为255，即对读取值不作修改
- WriteMask writeMask

当将值写入模板缓冲时，值会和writeMask进行按位与(&)操作，writeMask(范围0-255)，默认值为255，即对写入值不作修改
- Comp comparisonFunction

参考值和缓冲值要怎样比较，默认值为always，comparisonFunction可以取的值如下：

|  值   | 说明  |
|  ----  | ----  |
|  Greater  | 相当于 > 操作，即仅当左边>右边，模板测试通过  |
|  GEqual  | 相当于 >= 操作，即仅当左边>=右边，模板测试通过  |
|  Less  | 相当于 < 操作，即仅当左边<右边，模板测试通过  |
|  LEqual  | 相当于 <= 操作，即仅当左边<=右边，模板测试通过  |
|  Equal  | 相当于 = 操作，即仅当左边=右边，模板测试通过  |
|  NotEqual  | 相当于 != 操作，即仅当左边!=右边，模板测试通过  |
|  Always  | 不管公式两边为何值，模板测试总是通过  |
|  Never  | 不管公式两边为何值，模板测试总是失败  |

- Pass stencilOperation

当模板测试和深度测试都通过时，模板缓冲的值要怎么处理，默认值为keep，stencilOperation可以取的值如下：

|  值   | 说明  |
|  ----  | ----  |
|  Keep  | 保留当前缓冲中的内容，即stencilBufferValue不变  |
|  Zero  | 将0写入缓冲，即stencilBufferValue值变为1  |
|  Replace  | 将参考值写入缓冲，即将referenceValue赋值给stencilBufferValue  |
|  IncrSat  | stencilBufferValue加1，若值超过255，则取255  |
|  DecrSat  | stencilBufferValue减1，若值小于0，则取0  |
|  Invert  | 将当前模板缓冲值(stencilBufferValue)按位取反  |
|  IncrWrap  | 当前缓冲的值加1，若超过255，则变为0，然后继续自增  |
|  DecrWrap  | 当前缓冲的值减1，若小于0，则变为255，然后继续自减  |

- Fail stencilOperation

当模板测试不通过时，模板缓冲的值要怎么处理，默认值为keep

- ZFail stencilOperation

当模板测试通过，深度测试不通过时，模板缓冲的值要怎么处理，默认值为keep


### 判断依据
```csharp
if (参考值 & readMask comparisonFunction 缓冲值 & readMask) 像素通过

else 像素舍弃
```

因为readMask默认值为255，所以这时可以简化为：``if (参考值 comparisonFunction 缓冲值)``


## 实例
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


## 其它
[原文：Using the Stencil Buffer in Unity Free](https://alastaira.wordpress.com/2014/12/27/using-the-stencil-buffer-in-unity-free/?utm_source=tuicool&utm_medium=referral)

[官方文档：ShaderLab: Stencil](https://docs.unity3d.com/Manual/SL-Stencil.html)