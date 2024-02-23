TextMeshPro插件导入Unity后，其自带的默认字体只支持西文字符和一些标点符号，是不支持中文显示的。为了支持中文的显示通常有两种方案。
## 静态字体方案
Window->TextMeshPro->Font Asset Creator，打开如下界面：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220228184910.png)

Source Font File选中一个中文字库
Character Set选项选择Charactors from File
这样就可以通过配置一个中文文档来生成中文字库。

这种对于只有某些中文字符的项目是可行的，对于有聊天系统自然就行不通了。

## 动态字体方案
找一个带中文字符的ttf字库（推荐使用笔者在``浅谈Unity中的文字显示``一文中提到的DroidSansFallback字体），导入到Unity后，右键该字体:Create->TextMeshPro->Font Asset，即可创建该字体的.asset文件，这样在TextMeshPro-Text(UI)组件中，把Font Asset替换为上面生成的.asset文件即可。

也可以依旧使用默认.asset文件（LiberationSans SDF.asset），在默认asset文件的Fallback Font Assets列表中拖入上述生成的支持中文的asset文件即可。这样在识别到不认识的字符的时候会从Fallback中找。

需要注意的是默认情况下TMP只有一张字体图集，当字数过多时依旧会出现文字显示为方块的情况，这时候只需要设置字体asset的Multi Atlas Textures勾选上即可。