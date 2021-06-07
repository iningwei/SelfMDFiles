相机Clear Flags设置为 SolidColor；Background设置为白色，Alpha为0

显示RT物体的材质球使用 Transparent Shader

参考文档:[Clear flags in camera and render texture problem](https://answers.unity.com/questions/765394/clear-flags-in-camera-and-render-texture-problem.html)

## RT的深度缓冲
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210531181509.png)

大部分情况下需要设置为 At least 24 bits depth(with stencil)，当然在unity创建RT的资产的默认选项就是它，所以一般情况下不会出错。

但是当我们使用代码来创建RT的时候，就格外要注意设置该值，其支持0,16,24或者32这几个值，一般情况下至少要设置为24，否则模型在渲染为rt后会丢失深度信息，导致模型重叠显示。
可以在RenderTexture()的多态构造函数中传参，也可以在RenderTexture.GetTemporary()中传参。或者通过rt对象的depth属性来设置``rt.depth=24``。


