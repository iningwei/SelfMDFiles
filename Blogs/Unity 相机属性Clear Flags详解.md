Unity的Camera组件会根据物体的颜色缓冲、深度缓冲来决定以何种方式进行渲染，其中深度(Depth)指的是目标物体在世界坐标系中到相机的距离。
一般来说相机在渲染新的一帧时会清除全部或者部分老的图像信息，并把新的图像信息展示在老的图像信息上。而相机对信息的清除方法和其Clear Flags的类型有关。
- Skybox
当相机渲染新的一帧时，会清除上一帧每个像素的所有信息(颜色、深度)，然后再将需要渲染的颜色值与深度值(如有需要)写入。
由于 Skybox是最先被渲染的(默认 Queue=Background )，所以默认它们就作为了背景。
- Solid Color
当相机渲染新的一帧时，会清除上一帧每个像素的所有信息，然后把新的图像覆盖在具体颜色之上（颜色由Background决定，且Alpha设置无效）。
关于Background的说明：``The color applied to the remaining screen after all elements in view have been drawn and there is no skybox.``
- Depth only
当相机渲染新的一帧时，会清除上一帧的深度信息，并不会清除其颜色缓冲，因此新的图像会包含老的图像物体的颜色信息。老的图像因为没有深度信息，因此在表现上新老图像不会出现相交，老的图像只会被新的图像覆盖。
``清除深度信息后会发生什么？渲染过的像素如果没有需要渲染的物体，则会保持原来的颜色值；否则会渲染新的物体颜色值，不论这个点之前是什么(因为深度值被 Clear)，所有在这个像素上发生的 ZTest 默认规则下都会通过，ZWrite 也会按照设定的规则生效写入对应的深度值。``
当有多于一个相机对场景进行渲染时，通常会设置上层的相机的Clear Flags为Depth only，用来对相机进行分层。
``当我们需要使用多个 Camera 渲染不同的物体时，当在编辑器 Hierarchy 中为后面 Camera 的 Clear Flags 设定为该值时，该 Camera 渲染时由于不会清除之前 Camera 已渲染的图像(假定后面的 Camera 的 Depth 值大于前面的 Camera，后执行渲染)，并且由于清除了深度值，所以不会因为后面摄像机渲染的物体的深度值大于之前物体的深度值而不太能通过 ZTest，所以图像会覆盖在已有图像上方。``
- Don't Clear
当相机渲染新的一帧时，上一帧的的深度信息和颜色缓冲都不会清除。所以之前的颜色值会一直存在于屏幕上，直至新的颜色值替换掉缓存的颜色值。同理深度值也不会被清除，直至某个通过了 ZTest 并且 ZWrite 为 On 的像素将深度值更新。大致流程: 逐像素 ZTest，通过则写入新的颜色值，并根据是否开启 ZWrite 来确定是否更新深度值，否则舍弃。 
``Note that on some GPUs (mostly mobile GPUs), not clearing the screen might result in the contents of it being undefined in the next frame. On some systems, the screen may contain the previous frame image, a solid black screen, or random colored pixels``



对于Skybox和Solid Color模式，他们的相机的渲染流程大致为：逐像素过程中，由于像素的深度值被 Clear 了，根据使用的 Shader 中的设定规则如果 ZTest 通过，把要渲染的颜色值写入，深度值是否写入看使用的 Shader 中的 ZWrite 是否开启。由于 Skybox 或 Solid Color 是最先被渲染的(默认 Queue=Background )，所以默认它们就作为了背景。 

## 参考文档：
[unity mannual class-Camera](https://docs.unity3d.com/Manual/class-Camera.html)
[探寻 Unity Camera 属性之 Clear Flags](https://www.codercto.com/a/84393.html)