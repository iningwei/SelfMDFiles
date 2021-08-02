## 笔者环境
Unity2020.3.0f1
使用Profiler查看RenderTexture的释放

## 情形一：使用Temporary方式创建的RenderTexture
```csharp
rt = RenderTexture.GetTemporary(128, 128, 32, RenderTextureFormat.ARGB32, RenderTextureReadWrite.Default, 4);
rt.name="xxx";
```

Profiler中需要在Not Saved->RenderTexture节点下查看。（注意需要给创建的rt设置个名字，方便查看）

通过``RenderTexture.ReleaseTemporary(rt)``，在Profiler中查看到完全释放资源。
通过`` rt.Release()``，虽然可以释放资源，但是句柄依旧存在，在Profiler中显示占用内存为0B。
通过``GameObject.Destroy(rt)``则报错：Destroying object "xxx" is not allowed at this time.

## 情形二：使用构造函数的方式创建的RenderTexture
```csharp
rt = new RenderTexture(128, 128, 32, RenderTextureFormat.ARGB32, RenderTextureReadWrite.Default);
rt.name="xxx";
```

Profiler中需要在SceneMemory->RenderTexture节点下查看。（注意该节点下有一个4B内存，没有名字的静态引用节点，是系统自带的）

通过``rt.Release()``，在Profiler中查看到完全释放资源
通过``GameObject.Destroy(rt)``，亦可完全释放资源。这篇[文章](https://zhuanlan.zhihu.com/p/41251356)有提到GameObject.Destroy()为什么具有rt.Release()的效果，且还提到Destroy的方法可以彻底删除rt。对于该文的内容有一些疑惑，见下文。
通过``RenderTexture.ReleaseTemporary(rt)``，则报错：Attempting to release a non-temporary RenderTexture as a temporary texture.

## 一些疑惑
官方文档[RenderTexture.Release](https://docs.unity3d.com/ScriptReference/RenderTexture.Release.html)中提到这种释放方式只是释放了renter texture使用的资源。texture自己并没有销毁，在需要的时候会自动创建。
这段内容字面理解是rt.Release可以释放资源，但是无法销毁rt自己。但是我在Profiler中看到是完全销毁了的，没有看到相关引用存在。