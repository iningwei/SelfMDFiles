Unity中通过设置反射探针[Reflection Probe](https://docs.unity3d.com/2021.3/Documentation/Manual/class-ReflectionProbe.html)可以使使用了反射属性的物体材质更加酷炫。

常见的需求一般是在场景中设置探针，然后使用了反射材质的物体（比如：人物角色服饰等等）就可以反射出场景中的信息，增加了真实感。这是一种先有探针，后有依赖探针的物体的使用情形。

还有一种需求是先有物体，后设置探针。
比如对3D物体的RenderTexture立绘，往往3D物体并不是放在通用场景中的，比如放在一个专门用于立绘的小场景中，而这个场景并没有设置反射探针，那么就需要动态设置探针。相关代码如下：
```csharp
    public static void CharactorAddReflectionProbe(GameObject charactorObj, Action callAfterRender)
    {
        var probe = charactorObj.GetOrAddComponent<ReflectionProbe>();
        probe.size = Vector3.one * 5;
        probe.cullingMask = LayerIndexConfig.CharactorLayer;

        probe.mode = UnityEngine.Rendering.ReflectionProbeMode.Realtime;
        probe.refreshMode = UnityEngine.Rendering.ReflectionProbeRefreshMode.ViaScripting;
        probe.hdr = false;//建议关闭，部分低端机型不支持hdr，会出现黑块

        var renderId = probe.RenderProbe();//调用渲染方法，使反射Probe生效


        //RenderProbe()并不是同步的，需要耗一定时间
        //延时处理，判断probe渲染完毕后再显示目标立绘物体，避免视觉上的跳跃。
        TimerTween.Repeat(0.03f, () =>
        {
            if (charactorObj!=null&&probe != null && probe.IsFinishedRendering(renderId))
            {          
                if (charactorObj != null)
                {
                    if (callAfterRender != null)
                    {
                        callAfterRender.Invoke();
                    }
                }

                return false;
            }

            return true;
        }, false).Start();
    }
```