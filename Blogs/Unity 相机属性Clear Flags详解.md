Unity的Camera组件会根据物体的颜色缓冲、深度缓冲来决定以何种方式进行渲染，其中深度(Depth)指的是目标物体在世界坐标系中到相机的距离。
一般来说相机在渲染新的一帧时会清除全部或者部分老的图像信息，并把新的图像信息展示在老的图像信息上。而相机对信息的清除方法和其Clear Flags的类型有关。
- Skybox
当相机渲染新的一帧时，会清除上一帧的所有内容，然后把新的图像覆盖在Skybox之上。
- Solid Color
当相机渲染新的一帧时，会清除上一帧的所有内容，然后把新的图像覆盖在具体颜色之上。（颜色由Background决定，且Alpha设置无效）
- Depth only
当相机渲染新的一帧时，会清除上一帧的深度信息，并不会清除其颜色缓冲，因此新的图像会包含老的图像物体的颜色信息。老的图像因为没有深度信息，因此在表现上新老图像不会出现相交，老的图像只会被新的图像覆盖。
当有多于一个相机对场景进行渲染时，通常会设置上层的相机的Clear Flags为Depth only，用来对相机进行分层。
- Don't Clear
当相机渲染新的一帧时，上一帧的的深度信息和颜色缓冲都不会清除。``Note that on some GPUs (mostly mobile GPUs), not clearing the screen might result in the contents of it being undefined in the next frame. On some systems, the screen may contain the previous frame image, a solid black screen, or random colored pixels``


参考文档：[unity mannual class-Camera](https://docs.unity3d.com/Manual/class-Camera.html)