@[toc]
## 1：playing的时候改变了代码，重编译导致各种报错
Playing状态下，Unity3D编辑器默认会在检测到代码改变后，进行重新编译，然后继续运行。
可以通过**Editor->Preferences->General->Script Changes While Playing**
来设置Playing状态下，代码重编译的时机。


## 2：最大化窗口
Shift+space快捷键，可以最大化当前focuse的窗口

## 3：Animation窗口快捷键
在Animation窗口中，按下Ctrl+A，所有关键帧将集中显示在窗口中；选择某些关键帧，按下F键，可将它们居中显示在窗口中；按下C键，可以在曲线视图和关键帧视图间切换；按下K键添加关键帧。

## 4：设置编辑器运行一帧后暂停
先点击暂停按钮，再点击运行按钮，这样编辑器即会在运行一帧后暂停游戏

## 5：获得资源的预览图
``AssetPreview.GetAssetPreview(Object obj)``该方法只能在Editor下使用。
see:https://docs.unity3d.com/ScriptReference/AssetPreview.GetAssetPreview.html


## 6：展开Hierarchy树状图
alt+鼠标左键点击Hierarchy视图中的“三角符号”，即可完全展开节点的树状图


## 7：快速创建6边形，三角形等形状的sprite
Project视图下，鼠标右键Create->Sprite->各种形状
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190326182735463.png)

## 8：Reset()方法导致的锅
继承自MonoBehaviour的脚本，若在其内部定义方法Reset()
```
    private void Reset()
    {
    }
```
那么在脚本第一次拖拽到节点上会触发该方法，在组件点击弹出的选择项中选择Reset选项的时候也会触发该方法。
所以普通函数命名不要使用Reset。
这是因为Reset是MonoBehaviour下已定义的方法[MonoBehaviour.Reset](https://docs.unity3d.com/ScriptReference/MonoBehaviour.Reset.html)

## 9：调用Unity3D菜单项
EditorApplication.ExecuteMenuItem("GameObject/Create Other/Cube");

## 10：Scene视图单独显示目标对象
该功能准确叫法是Isolation View，在层级视图选中目标GameObject后，快捷键Shift+H便可开启ISolation View，Scene视图中将会强制隐藏场景中的其它部分，直到退出

## 11：对象能见度设置和对象选取设置
能见度设置可以设置对象在Scene视图是否可见，方便编辑
选取设置可以设置对象可否在Scene视图中被选中，可以有效避免误选和误编辑

## 12：快速搜索
自Unity2021.1起，QuickSearch功能已整合编辑器中。通过Edit->Search All（或在Windows按Ctrl+K、在macOS中按下Cmd+K）即可开始搜索。
支持引擎内各种内容的搜索，包括资源、场景对象、菜单选项、软件包、API、各种设置等等
