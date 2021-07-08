获得物体的材质球可以使用render.material和render.sharedMaterial两种方式。
其中第一种会在第一次调用的时候自动实例化一个材质球，并自动赋给该render，后续若无其他特殊操作，再使用render.material都是使用第一次实例化的材质球。
第二种是直接操纵该render引用的材质球，这个材质球也有可能会其它物体使用。这种方式对该材质球的修改，也会影响到其它物体的显示效果。

在很老版本的Unity中要对使用同一个材质球的物体修改材质球属性，只有使用render.material的方式，生成每个render自己独有的材质球，这样的话势必会造成dc的增加。

后来Unity引入了MaterialPropertyBlock类，通过render.SetPropertyBlock(MaterialPropertyBlock block)或render.SetPropertyBlock(MaterialPropertyBlock block,int matIndex)的方式即可实现对某物体材质球属性的修改，同时又不会影响到其它使用了该材质球的物体。

示例代码如下：
```csharp
MaterialPropertyBlock properties = new MaterialPropertyBlock();
properties.SetColor("_Color", new Color(1, 0, 0));
render.SetPropertyBlock(properties);
```

需要注意的是该方式并不支持UGUI，在UGUI中只有通过多个材质球来实现属性的修改。（比如为UGUI元素添加了mask组件后，其实内部也是生成了一个依然使用UI-Default.shader的新材质球，该材质球有新的属性设置）

## 补充
经过笔者测试，对sampler2D类型的属性设置为null，运行时会报错，代码如下：
```csharp
MaterialPropertyBlock properties = new MaterialPropertyBlock();
properties.SetTexture("_MainTex", null); 
renderer.SetPropertyBlock(properties);
```
https://forum.unity.com/threads/materialpropertyblock-can-not-set-texture-null.1137178/