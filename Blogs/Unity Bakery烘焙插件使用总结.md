Bakery版本：1.9


烘焙时参数设置有几点要注意
1，渲染用的平行光添加了BakeryDirectLight后，该平行光Mode参数要设置为Baked，否则渲染出的结果偏差很大。
2，Unity的天光、环境光在烘焙时并不生效。需要添加Bakery的Skylight，否则烘焙出来的场景会偏暗。
可以不用设改变Unity的天空盒适配Bakery的Skylight，否则场景烘焙出来会有Skylight带来的天空色。
只需要调节Skylight的Intensity，来达到合适的环境效果即可
3，最终运行场景时，若有Mixed需求（比如对部分动态物体需要动态阴影效果），则烘焙时Bakery面板需要选择渲染模式(Render mode)为Subtractive（参考这里：[Lighting Mode: Subtractive](https://docs.unity3d.com/Manual/LightMode-Mixed-Subtractive.html)），运行时主灯Mode设置为Mixed。