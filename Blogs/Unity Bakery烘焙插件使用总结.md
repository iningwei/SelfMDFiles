Bakery版本：1.9

笔者项目情况：
1，美术工程和程序工程分离，且Editor都设置的为Anroid平台
2，场景烘焙后，对场景打ab，烘焙贴图随着场景一起被打入到ab中，未做针对性剥离
3，程序工程通过ab加载资源
4，美术工程中导入插件后会自动设置宏BAKERY_INCLUDED，程序工程未导入Bakery插件任何内容

项目中遇到的问题：
1，程序工程的客户端下跑显示上没有任何问题，只是场景加载后会默认多一个节点!ftraceLightmaps,该节点上有一个叫ftLightmapsStorage的脚本丢失。
2，apk会有如下日志：
The referenced script (Unknown) on this Behaviour is missing!
The referenced script on this Behaviour (Game Object '<null>') is missing!
导致相机在特定视角下的Mesh会出现漏面、叠面等异常情况。
经过排除感觉是后处理的问题：游戏中有后处理节点，没有开启；删除后没遇到了。

