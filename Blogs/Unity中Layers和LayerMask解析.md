Unity中一共有32个Layer（索引范围：0-31），默认层为Default。
Unity中使用int32表示32个Layer

## 位运算符
位运算符：``~、|、&、^、<<、>>``
逻辑运算符：``&&、||、!``

对一个十进制数使用左移运算符，结果为该数乘以2的N次幂。如：
``var tmp=14<<3``
结果为14*(2 * 2 * 2)=112
同理右移运算符会对原来的数除以2的N次幂。

## LayerMask
在Unity中使用到Layers的地方包括相机的渲染、灯光的照射、碰撞检测等等。
### API
静态方法LayerMask.NameToLayer("layerName")根据Layer名获得其索引。
静态方法LayerMask.LayerToName(layerIndex)根据Layer索引获得其名称
LayerMask的实例属性value，获得的结果为``1<<该实例的索引值``
### 开关
Unity中使用LayerMask来控制Layers层的开关。如
LayerMask mask=1<<2;表示开启Layer2，其它层关闭
LayerMask mask=1<<2|1<<8;表示开启Layer2和Layer8，其它层关闭
LayerMask mask=~(1<<3|1<<6);表示关闭Layer3和Layer6，其它层开启
LayerMask mask=0;全部关闭。



## LayerMask控制Layers层开关的原理
Unity提供索引从0-31层级的Layer，用int32表示。
```
Layer0对应值为1<<0，即1
Layer1对应值为1<<1，即2
Layer2对应值为1<<2，即4
Layer3对应值为1<<3，即8
Layer4对应值为1<<4，即16
Layer5对应值为1<<5，即32
.
.
.
Layer31对应值为1<<31，即2,147,483,648‬
```
比如我要启用Layer1和Layer4。那么mask=1<<1|1<<4，即为2|16。转换为2和16的二进制按位或运算``0000 0010|0001 0000``，结果为mask=18。
可见18这个结果只有可能是16和2两个数相加得到的。其它层无论怎么相加都获得不到18这个值。基于这个道理可以用或运算符控制目标层的开启。
基于上述二进制位运算的过程，可以很容易搞明白取反运算符``~``的作用原理