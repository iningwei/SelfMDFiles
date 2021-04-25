笔者项目有个碰撞检测的需求，同一时刻有三次检测，且是不同的相机通过ScreenPointToRay()来进行对不同Layer的检测。

编辑器下一切OK，打包到设备上有个相机死活检测不到目标碰撞物体。
由于笔者这边是事件触发后的检测，不是Update检测，因此不存在要把检测放到FixedUpdate中的问题。

## 原因
编辑器开启了Strip Engine Code导致。

详述：笔者项目资源是通过AB进行加载的，目标检测物体挂载的是CapsuleCollider，因为工程内没有直接引用到，故在Strip Engine Code时剥离了这部分代码，导致射线检测和编辑器内不一样。

其实真机日志已经打出来了提示，因为日志太多，所以没有注意到，这个后面一定要注意日志的反馈。
真机日志为：
```
Could not produce class with ID 136.
GameObject contains a component type that is not recognized
```
这里：[YAML Class ID Reference](https://docs.unity3d.com/Manual/ClassIDReference.html)，可以查到136刚好对应的是CapsuleCollider

## 解决方案
### 开启了Strip Engine Code
若是有需求对包体大小很敏感，需要开启Strip Engine Code。那么就需要让编辑器在打包的时候知道你使用了哪些类，避免被裁剪。

- 非UnityEngine.dll中的类

那么在代码中new一下该类即可实现引用

- UnityEngine.dll中的类

如果资源或者代码没有直接引用的话，需要在link.xml中添加该id（比如笔者这种情况）

id:

https://docs.unity3d.com/Manual/ClassIDReference.html

方法:

https://docs.unity3d.com/Manual/IL2CPP-BytecodeStripping.html

- UnityEditor.dll中的类

Unity中有个特例，AnimatorController,它位于UnityEditor命名空间下，但是在真机运行时也会用到。
如果其被裁减掉，那么运行时会报错：Could not produce class with ID 91(AnimatorController)，而且该类无法使用link.xml的方式。可以在Resource下建一个空的prefab,在上面挂一个AnimatorController,再打包来进行引用

https://forum.unity.com/threads/webgl-could-not-produce-class-91-engine-stripping.467181/

### 关闭Strip Engine Code
懒人的解决方法
