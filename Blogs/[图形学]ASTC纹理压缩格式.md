## 纹理压缩的目的
1，降低内存，特别是移动端应用，内存占用不应过大，否则低端机很容易崩溃
2，降低带宽，手游类应用，在渲染时会有大量贴图传输到GPU，不限制的话不仅会严重影响渲染性能，同时会带来很严重的发热

因此我们需要一种内存占用既小又能被GPU读取的格式——压缩纹理格式。纹理压缩对应的算法是以某种形式的固定速率有损向量量化(lossy vector quantization )将固定大小的像素块编码进固定大小的字节块中。
有损表示对于渲染来说，有损压缩是可以接受的，一般选择压缩格式时需要在纹理质量和文件大小上寻求一个平衡。
固定速率压缩指的是什么呢？因为GPU需要能够高效的随机访问一个像素，这意味着对任意像素，解码速度不该有太大的变化。因此，我们常见的贴图压缩算法都是有损压缩。相反的例如zip则是一种可变速率压缩。
向量量化(Vector quantization, VQ)是一种量化技术，将一组大量的点(向量)分成具有近似相同数量的最接近它们的点的组。每个组用它的质心点表示，因此存在数据误差，适用于有损压缩。放到纹理压缩中来理解，就是例如将4x4块像素的颜色以2个基色来表示。

编码和解码速度。一般来说编码速度慢没关系，因为通常纹理压缩只需要在游戏打包时进行一次，对于用户运行时体验完全没有影响。但解码速度必须足够快，而且基本上不能影响到渲染性能。

压缩比：通常以比特率或每像素的平均比特数(bits per pixel, bpp)表示，常见的为2~8bpp。一般RGB原生纹理的像素指24位，4bpp表示每像素占4位，所以也可以认为4bpp表示压缩比为6:1。

## ASTC纹理压缩格式简介
ASTC(Adaptive Scalable Texture Compression),由ARM和AMD联合开发，2012年发布。是一种基于块的有损压缩算法。它的压缩分块从4x4到12x12最终可以压缩每个像素占用1bit以下。
ASTC格式在具体的Block Size下对RGB和RGBA压缩是一致的。
ASTC格式适用于POT和NPOT纹理。

![astc block size](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/astc%20block%20size.png?raw=true)
上图展示了不同Block Size下每个像素占用的bits。易知，Block Size越大，压缩的越厉害。
以ASTC 4\*4 Block Size为例，可以看到每个像素占用8bits即1个字节。因此一张1024\*1024的RGBA图片按照该格式压缩后占用的内存大小为1MB。

nvidia官方对ASTC格式有详细的说明：[astc-texture-compression-for-game-assets](https://developer.nvidia.com/astc-texture-compression-for-game-assets)

## 适配机型
1，iOS
苹果从A8处理器开始支持ASTC，即iPhone6和iPad mini 4及以上的设备都支持，ASTC格式在iOS设备上的显示效果比PVRTC的效果要好很多(PVRTC格式存在两个大问题：首先是透明贴图在iOS上显示比较模糊，失真；另一点是对于颜色比较丰富的图，特别是UI，颜色过渡大的区域会出现色阶问题，目前的方案一般是拆分Alpha通道，见这里：[IOS下拆分Unity图集的透明通道（不用TP）](https://zhuanlan.zhihu.com/p/32674470)),[Unity 分离贴图 alpha 通道实践](https://indienova.com/indie-game-development/unity-alpha-separate/)。因此在当前情况下iOS上可以全部使用ASTC作为纹理格式。

2，安卓
安卓中所有支持OpenGL ES 3.1及以上的设备，和大部分支持OpenGL ES 3.0的设备都支持ASTC。因此在安卓上需要根据具体情况来设置纹理压缩格式，一般而言若项目依旧要考虑第三世界国家和低端机型，就要退而求其次使用ETC2格式进行压缩。

如果GPU不支持的话一般会解压为RGB或者RBGA，Unity官方对纹理压缩格式的选择和底层的一些异常处理都做了详细的说明：[Recommended, default, and supported texture formats, by platform](https://docs.unity3d.com/Manual/class-TextureImporterOverride.html)

## 其它
- POT 和 NPOT
虽然ASTC支持POT和NPOT，但是在项目中能使用POT的情况下尽量使用POT，这涉及到更复杂的图形学原因。这里引用一下一位知乎匿名用户的解释,[OpenGL支持非二次幂纹理的底层原理是什么？](https://www.zhihu.com/question/376921536/answer/1063272336)
>这个问题其实与GPU的寻址方式（地址对齐要求）有关，并非是图形API层面的限制。</br>
>GPU作为一种专用的绘图硬件（当然现在也在逐渐泛用化），其高速运作的能力来自于对于特殊目的定制的特殊硬件模块。其中，实现贴图参照的模块就是这么一种特殊的硬件模块。
>在CPU当中，当要参照一张贴图的时候，你需要在程序当中给出计算地址的公式。比如，当你要访问一张宽度为w高度为h的以行优先顺序存储的贴图当中的坐标为（x，y）的贴图的时候，就需要用y*w+x来计算这个地址。这个计算公式是以代码的形式给出的，对于CPU来说，它只是将这张贴图作为一个连续的内存空间处理而已。</br>
>但是GPU不同。GPU当中完成对贴图访问（寻址以及获取数据）的模块是以硬件形式存在的。它是不可编程的，你只能从它所提供的几种方式当中选一种。这是第一个因素。</br>
>其次，GPU当中进行的是大量的并行计算。这就意味着GPU经常会需要对贴图的不同位置进行同时参照。而且，这种参照往往是需要截取贴图当中一小块面积的内容，也就是是一种2D甚至是3D形状的参照，按CPU那种1D线性的方式保存贴图的话，会使得要参照的那一小部分地址不连续，从而影响高速缓存的命中率，降低速度。
>所以，贴图在GPU的内存（显存）当中，一般都不是以行优先或者列优先方式存储的，而是以“块”为单位存储的。就好像一块一块马赛克组成的墙面那样，每个马赛克对应的像素在内存上是连续存储的。这是第二个因素。</br>
>此外，为了尽可能节约内存开销和传输带宽，贴图一般都会以一种合适的压缩格式存储。而常用的压缩格式，如BC或者DXT，都是将贴图分块进行压缩。这同样隐含了贴图必须是这些“块”的整数倍这样一个条件。这是第三个因素。</br>
>上述几个因素（硬件寻址+贴图在内存上的特殊排布方式+压缩算法要求）决定了一款GPU在设计的时候就会将这个“块”的最小尺寸固定下来。有的GPU是``2x2像素``，有的是``4x4像素``，还有的是``8x8像素``。所以当贴图的尺寸不是这些“块”的整数倍的时候，当贴图被传送到GPU内存（显存）的时候，就会被拉伸或者在四周（一般是右侧和下侧）填充无用数据，使其成为这些“块”的整数倍。（称为pitch）
>这个情况是GPU硬件的要求，与使用何种图形API无关。但是诸如OpenGL这种抽象等级较高的图形API在易用性和可控性之间选择了易用性，也就是尽力隐藏这些细节，在其内部为你完成必要的拉伸或者pitch的操作，从而使得你觉得好像它支持非二次幂的纹理。

- 为什么我们不使用png、jpg这类常见的压缩格式？
尽管像jpg、png的压缩率很高，但并不适合纹理，主要问题是不支持像素的随机访问，这对GPU相当不友好，GPU渲染时只使用需要的纹理部分，我们总不可能为了访问某个像素去解码整张纹理吧？不知道顺序，也不确定相邻的三角形是否在纹理上采样也相邻，很难有优化。这类格式更适合下载传输以及减少磁盘空间而使用。

- 其它移动端压缩格式小结
1，安卓
ETC：也叫ETC1，被所有安卓设备支持，不支持透明通道，只有RGB bpp(bits per pixel)为4的压缩方式。
ETC2：OpenGL ES 3.0及以上的设备支持，其有针对RGB，bpp值为4的压缩方式；也有针对RGBA，bpp值为8的压缩方式。项目中可以根据是否带透明通道，选择相应的压缩方式，进一步降低尺寸。
2，iOS
PVRTC：也针对RGB和RGBA提供了不同的压缩方式，但是二者的bpp确是一致的，比如RGB bpp为4以及RGBA bpp为4的这两个格式。可见由于RGBA bpp只有4，相对ETC2的RGBA bpp为8的压缩方式少了一半，（虽然不知道PVRTC内部的压缩算法细节，PS:PVRTC不开源）因此才会导致PVRTC在iOS上带透明通道时效果很差。且PVRTC还要求图片必须满足POT。


## 参考文档
- [ASTC纹理压缩格式详解](https://zhuanlan.zhihu.com/p/158740249)
- [Compressed GPU texture formats – a review and compute shader decoders – part 1](https://themaister.net/blog/2020/08/12/compressed-gpu-texture-formats-a-review-and-compute-shader-decoders-part-1/)
- [你所需要了解的几种纹理压缩格式原理](https://zhuanlan.zhihu.com/p/237940807)
- [各种移动GPU压缩纹理的使用方法](https://www.cnblogs.com/luming1979/archive/2013/02/04/2891421.html)