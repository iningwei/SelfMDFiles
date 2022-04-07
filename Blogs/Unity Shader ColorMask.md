ColorMask指令用于控制输出颜色中各个通道的写入，默认情况下所有的通道(RGBA)都会被写入。一些特殊情况下可能会希望某些通道不被写入:比如实现阴影时肯定不希望阴影是带颜色的;模版缓冲通常用来存储一些特定数据，而不希望颜色被写入，因此通常会设置ColorMask 0

## 渲染管线兼容性
兼容Unity目前支持的所有的渲染管线（Build-in Render Pipeline, URP, HDRP,Custom SRP）

## 语法
ColorMask <channels>
ColorMask <channels> <render target>
指令可以写在Pass内，只影响某个Pass；也可以写在SubShader块内，那么会影响SubShader内所有的Pass。
render target用来设置渲染目标索引，值为0到7。只有在使用MRT（multiple render target）时才有用。
channels可以设置为0（R、G、B、A 4个通道都不写入），或者R、G、B、A的任意组合。

## 其它
1，ColorMask生效时机为一个Shader进行Frag输出的时候，若设置的ColorMask不是RGBA，比如设置的ColorMask为R，那么目标的最终渲染时颜色并不是（R,0,0,0），缺失的GBA三个通道会从相机的背景取，最终的颜色是它们混合的结果。(Unity测试验证是这样的，具体文档没看到哪里有写)
2，ColorMask的设置不会影响深度写入，比如对某个物体A的渲染设置了ColorMask 0，那么其将不会写入颜色，但是在默认情况下由于它的深度是写入了的，因此若其遮挡了另一个物体B的一部分，那么物体B被遮挡的部分由于无法通过深度测试将不显示。