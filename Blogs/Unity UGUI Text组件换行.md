## 法一
直接在Text组件的Inspector面板里面通过键盘Enter键换行
## 法二
使用\n换行符
使用代码``.text="xxxxx\nxxxx";``可以实现换行.
需要注意的是在Inspector面板通过添加\n换行符在预览阶段是无法换行的，而且运行后也无法换行。
这是因为运行后\n字符变成了\\n，因此需要转换一下，如下：
```csharp
myText.text=myText.text.Replace("\\n","\n");
```

PS:以上对TextMeshPro-UGUI插件也适用