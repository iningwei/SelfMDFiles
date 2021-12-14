一般在Unity内部一些内置shader是的路径是以Hidden开始的，在Unity编辑器下材质球的下拉列表中不会被看到。这些shader一般是比较固化的，不希望暴露给用户的，同时也使得shader下拉列表更简洁。

用户自己也可以定义路径以Hidden开始的shader，通常用于后处理类中（一般比较固化）。

## 示例
可以参考开源项目：[KinoBokeh](https://github.com/keijiro/KinoBokeh)中的ColorSuite后处理。需要注意的是其通过ColorSuiteEditor.cs对ColorSuit.cs进行了Editor样式处理，隐藏了对shader变量的赋值。
ColorSuite.cs.meta的文件内容为：
```c
fileFormatVersion: 2
guid: 10593b7d510b64560a297a8af1356dcb
MonoImporter:
  serializedVersion: 2
  defaultReferences:
  - shader: {fileID: 4800000, guid: dc9775b65c52747e69fc4c854c00d696, type: 3}
  executionOrder: 0
  icon: {instanceID: 0}
  userData: 
  assetBundleName: 
  assetBundleVariant: 
```
其中有对shader引用的一行``- shader: {fileID: 4800000, guid: dc9775b65c52747e69fc4c854c00d696, type: 3}``,这里指定的guid值即为对应shader的guid值（项目中因为资源移动、改名、复制粘贴等原因可能会导致目标shader的guid值发生变化，若原始cs.meta文件中的没有跟着变化，会导致找不到目标shader，这个时候就要手动配置一下或者通过Inspector面板的debug模式重新拖拽一下目标shader）。
对于KinoBokeh项目，若要在ColorSuite面板中对shader赋值，可以通过Inspector面板的debug模式解决。