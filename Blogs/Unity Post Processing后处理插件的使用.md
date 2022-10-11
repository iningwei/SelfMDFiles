Post Processing是Unity官方提供的内置后期效果增强组件。Package Manager搜索关键字Post Processing，并安装。

## Post-process Layer
在相机上挂载Post-process Layer组件：
![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/postprocess-layer.jpg?raw=true)
Trigger(触发器)一般设置为相机本身。有需要的话也可以设置为其他物体，比如挂载到角色上，可以实现进入特定区域触发特效。
Layer是一个很重要的设置，必须保证Post-process volume组件的层级与此项相同。

## Post-process volume
新建个空物体（注意要设置该物体的Layer和Post-process Layer中的Layer一致，后处理效果才能生效），挂在组件Post-process volume:
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220912003619.png)
IsGlobal:控制该volume是全局还是局部。局部的话，需要挂在Collider3D组（并勾选Is Trigger）,当上述提到的Post-process Layer中的触发器进入Collider中即可看到效果，可以实现洞穴等场景的变换效果。
默认情况下Volume中已经自带了几种效果，还可以通过Add effect按钮，添加更多的效果。

### 特效
- Ambient Occlusion
- Auto Exposure
- Bloom
- Chromatic Aberration
- Color Grading
- Depth of Field
- Grain
- Lens Distortion
- Motion Blur
- Screen Space Reflections
- Vignette

