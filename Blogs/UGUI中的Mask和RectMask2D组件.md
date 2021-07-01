二者都是UGUI中提供的裁切工具，通过UI-Default这个shader来进行裁切功能的实现，但是实现原理、使用方式都不尽相同。

## Mask
- 原理
Mask使用Stencil的方式进行裁切，其依赖一个Image组件，裁剪区域即为该Image组件范围

- 性质
1，单个Mask会多出两个dc，一个是Mask组件自身的dc(一般称之为首)，一个是Mask遍历完子节点后生成的dc（一般称之为尾）。
2，多个Mask若满足合批条件（``同Render Depth、同Material、同Atlas``），则多个Mask的首和首， 尾和尾是可以合批的。但是首和尾是肯定不能合批的。
3，Mask内的UI和外部UI不可合批，多个Mask内的UI若满足合批条件则可合批。
4，Mask嵌套不能超过9层

## RectMask2D
- 原理
RectMask2D通过承载该组件的物体的长宽尺寸来控制其子物体的显示区域。

- 性质
RectMask2D下的所有子节点都无法与外界UI节点和批，且多个RectMask2D之间不能合批。


- 限制
只支持2D空间
对于不在一个平面的元素，可能会出现裁切问题

- 优势
不需要依赖Image组件进行裁剪，其裁剪区域就是自身rect的范围
没有使用模板缓冲
其自身不会产生额外的draw call
没有材质球改变
性能更好

## 其它
默认情况下Mask是硬裁切，RectMask2D支持软裁切

## 参考
[Unity 3D - Mask和RectMask2D区别](https://www.pianshen.com/article/21261176829/)
