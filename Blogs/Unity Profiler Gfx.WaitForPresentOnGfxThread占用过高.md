当在Profiler CPU项中Gfx.WaitForPresentOnGfxThread 这个项占比过高时，往往代表Unity的主线程已经跑完了，但是在等待GPU的渲染完成，这样做是为了达到同一的帧数。

至于为什么Render Thread会很慢，需要通过查看GPU Profile或者结合项目实际情况去分析。

比如场景开启了事实光照和阴影，在出现密级阴影时会消耗大量GPU。

参考文档：[Common Profiler markers](https://docs.unity3d.com/Manual/profiler-markers.html)