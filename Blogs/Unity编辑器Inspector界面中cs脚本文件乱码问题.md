既往经验通常都会在模版文件夹``Editor\Data\Resources\ScriptTemplates``下修改模版文件的编码格式为utf-8编码。
但是使用vs编辑代码写入中文保存后，文件编码格式变成ANSI，导致在Unity编辑器中的中文是乱码显示。
其实根源是：
```
在VS为UTF-8 BOM的文件添加中文，保存后格式仍然为UTF-8 BOM。
在VS为UTF-8的文件添加中文，保存后格式被修改成了ANSI或GB2312。
```
因此如何设置VS保存文件的格式才是最根本的解决方法。

## 设置VS保存文件的格式
[VS saves files without BOM as ANSI instead of UTF-8](https://developercommunity.visualstudio.com/t/vs-saves-files-without-bom-as-ansi-instead-of-utf/364271)
通过为VS工程配置".ediotrconfig"文件即可解决。
右键 解决方案资源管理器->添加->New EditorConfig，即可创建一个空的.editorconfig文件。添加如下内容：
```
# All files
[*]
charset = utf-8
```

## 其它
[在 Visual Studio 中使用 EditorConfig 统一代码风格（含原生与插件）](https://www.codercto.com/a/39910.html)

[EditorConfig 使用详解](https://blog.csdn.net/lwx931449660/article/details/120451417?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0-120451417-blog-119480088.pc_relevant_aa_2&spm=1001.2101.3001.4242.1&utm_relevant_index=3)