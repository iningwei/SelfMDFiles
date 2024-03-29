HDR（High-Dynamic Range）是高动态范围的缩写，何为高动态范围，这里举例说明：
现实真正存在的亮度差，即最亮的物体亮度和最暗的物体亮度之比为10<sup>8</sup>， 而人类的眼睛所能看到的范围是10<sup>5</sup>左右，但是一般的显示器、照相机能表示的只有256种不同的亮度。
但是我们可以多拍几张照片，2张，3张……甚至几十张，这些照片的曝光依次增大，很多朋友应该可以想到，随着照片曝光的增大，照片会依次变亮，换一种角度，照片所表示的细节会由亮处向暗处改变。
根据上面的原理，如果我们将照相机拍摄的很多张图片合成，结果图片的数量级一般为2<sup>16</sup>甚至更多。
这样问题就出现了，2<sup>16</sup>或者更高数量级的亮度只能存在电脑里，而一般的显示器只能表示2<sup>8</sup>个亮度数量级，用256个数字来模拟2<sup>16</sup>所能表示的信息，这种模拟的方法就是HDR技术核心内容之一，学名叫Tone-Mapping（色调映射）。用Tone-mapping压缩以后，合成的HDR影像就能很好地在显示器上显示。


Dynamic Range（动态范围）是指一个场景的最亮和最暗部分之间的相对比值。HDR图片是使用多张不同曝光的图片，然后再用软件将它们组合成一张图片，相比普通图像，其可以提供更多的动态范围和图像细节。

## 实例
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210914122816.png)

上面大图为合成后的HDR图，下面三张小图为原始的多张不同曝光度的图。


## Unity中
Unity中默认使用的是LDR即Low Dynamic Rangle低动态范围。每个颜色数据（红色、绿色和蓝色）使用8bit存储颜色，为每个颜色数据提供256种可能的组合。这样我们能够引用超过1600万种颜色，从黑色到白色的颜色范围。
现实世界的颜色远远超出了1600万这个范围。Unity能够处理非常明亮的灯光，产生的颜色超出LDR设备（如电脑屏幕）的颜色。尽管在输出设备中存在这种潜在的限制，但是在许多应用中，这些极值的光仍然对我们有用。

通过在场景相机上启用HDR，将会以更高的精度去存储颜色（使用浮点表示）。

HDR使我们能够在我们的场景和阴影区域之间保持亮度的巨大差异，例如户外照明。我们还可以通过在场景中对这些鲜艳的颜色进行应用应用，创造出“绽放”或发光效果。这样的特殊效果，可以增加场景的现实感。

需要注意的是部分低端老旧的移动设备不支持HDR。Unity中虽然提供函数SystemInfo.SupportsRenderTextureFormat(RenderTextureFormat.DefaultHDR)进行兼容性检测，但是通过一些网友反馈该API的返回值并不准确。所以在移动设备上慎用HDR。