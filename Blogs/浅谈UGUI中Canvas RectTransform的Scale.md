## 前言
Canvas的Rect Transform组件的Scale值和Canvas组件的Render Mode设置有关。

当为``Screen Space - Overlay``时Scale值为1，不可更改。

当为``World Space``的时候，Scale值默认为1，但是可以修改。

下面具体总结一下Render Mode为Screen Space - Camera时，Scale值的计算方式。

## Screen Spcae - Camera模式下的Scale值
该模式下Scale值是由引擎内部自动计算得到的，且不能修改。

该模式依赖一个Camera，通常情况下在UI系统中我们会使用正交相机。下文详细探讨正交相机模式下的Scale值，透视相机后面有空再推导。


假设当前屏幕的高度为ScreenH，正交相机有个用户可以设置的参数Size，那么有公式如下：

```csharp
ScreenH*Scale*(1/2)=Size
```

可以得到：``Scale=(2*Size)/ScreenH``

## 对比Sprite Renderer
在做纯2D游戏时，多使用Sprite Renderer组件，一般也使用正交相机，相机的Size设置规则为：``Size=设计分辨率高度/100/2``

## 建议
使用UGUI开发时，UI相机的Size不要随意设置。使用规则：``Size=设计分辨率高度/100/2``
