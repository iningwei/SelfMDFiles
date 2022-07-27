## Unity内存分类
Unity内存分为两大类，即Mono内存和Native内存

Mono内存是只增加不减少的，当app发现Mono内存不够使用时，会自动扩容（具体值文档未查到，大概10MB），用完后归还的内存也不会释放给系统，只会规范给Mono内存池。简单来说一些System命名空间下的API对内存的使用即消耗的Mono内存。
Mono内存使用的是托管内存，由底层GC进行自动的释放。

通过UnityEngine命名空间中的接口分配的内存，其底层是C++实现的，分配在Native堆内存上的。Mono内存可以通过GC自动释放，Navtive内存的释放需要调用``Resources.UnloadUnusedAssets()``(注意，该API在内部是调用了GC.Collect()的)



Native内存卸载示意图：

![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/unityneicun.png?raw=true)



## 内存泄漏

## 内存优化

