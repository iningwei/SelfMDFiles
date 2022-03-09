笔者最近项目使用TextMeshPro完成图文混排的需求，以及图文超链接，总的来说功能很强大，没遇到什么坑。
这里记录一下图文混排实现的基本流程流程。
## 制作需要混排图片.asset文件
### 方式一
使用图集生成SpriteAsset，需要保证图集的Mode是Multiple类型，即已经裁分了精灵。
右键图集文件选择Create->TextMeshPro->Sprite Asset,会自动生成和图集同名的.asset文件。.asset文件需要放置到TextMesh Pro/Resources/Sprite Assets目录下。
.asset文件文件通过对图集文件引用，并包含图片对应的字符表（Sprite Character Table）和图片对应的字形表（Sprite Glyph Table）。这两个表用户都可以可视化编辑的。

### 方式二
使用TexturePacker软件，输出格式选择JSON(Array)，拖入目标图片或者包含目标图片的文件夹。
Publish后会生成json文件和png文件各一份。这两个文件导入Unity内。
Unity内选择Window->TextMeshPro->Sprite Importer，打开Sprite Importer界面。拖入上述两个文件到指定位置，点击Create Sprite Asset即可生成会在内存中预生成，再点击Save Sprite Asset即可生成图文混排需要的.asset文件。.asset文件需要放置到TextMesh Pro/Resources/Sprite Assets目录下。


## 实现图文混排
### 引用关系设定
- 单独指定引用
在TextMeshPro组件Extra Settings选项中的Sprite Asset添加对具体.asset文件的引用。
- 全局设定引用
Project Settings->TextMesh Pro->Settings->Default Sprite Asset内为图文混排的全局设定，即若在TextMeshPro组件中未特殊指定，会依这里的配置为准。
### 代码调用
- <sprite=具体索引数值>

- <sprite name=\"具体图片名\">

- \U000``Unicode``

可以通过上述三种方式调用。
需要注意的是笔者通过方式一和方式二生成的.asset文件中图片的Unicode都是一样的，且不可修改，因此无法使用Unicode方式。但是使用TextMeshPro的自带资源的Unicode是可以图文混排的。``不知道这里设置上还有哪里没有搞对！！！``

另外使用Unicode调用的时候一定要选中.asset文件中的某个图片查看其Unicode，否则直接显示的Unicode多个前缀0x

### 图片大小，偏移设置
默认情况下图片可能会出现偏移问题，大小也可能不合适，可以通过在.asset文件的Global Offsets&Scale区域进行整体设置参数。对于特殊的图片要单独设置的话可以选中它再特殊设置，或者通过文本文件打开.asset文件来编辑。






### 注意事项
最好保证小图尺寸一致，这样可以进行统一的缩放设置