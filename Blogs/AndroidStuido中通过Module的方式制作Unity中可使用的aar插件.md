## 流程
- 创建Module，选择 ``Android Library``，然后输入Module name，形如``:app:名字``。
- 创建好后可以删除用不到的目录及资源，但是AndroidManifest文件不可删除，否则打包会报错。
- Unity2021.2及之后已不支持在Unity中直接使用Plugins/Android/res这种目录了，有这种需求的需要把资源做到aar中。这样的话就要在Module中右键``->New->Folder->Res Folder``创建res节点，然后把需要的资源粘贴到res节点下即可。

## 输出aar
选择目标Module，菜单栏选择 ``Build->Make Module '某具体Module名'``
切换到Project视图，输出的aar位于对应Module下的``build->outputs->aar``目录下
