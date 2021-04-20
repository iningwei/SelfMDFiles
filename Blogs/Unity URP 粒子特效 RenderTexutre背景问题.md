## 说明
项目使用了URP，使用RT的方式播放特效。播放RT的材质球的Shader使用了Universal Render Pipeline/Lit, Workflow Mode为Specular、Surface Type为Transparent

某特效是由普通Mesh和Unity粒子特效构成。

用以渲染RT的相机设置了BackgroundType为Solid Color，并设置Backgroud为White，且其Alpha为0。出现的问题是：在Mesh前面的粒子被渲染了，在Mesh周围未被遮挡的粒子不显示。

## 解决方法
设置Unity粒子特效的材质球Shader为``Universal Render Pipeline/Particles/Lit``或者``Universal Render Pipeline/Particles/Simple Lit``即可。

笔者用的``Universal Render Pipeline/Particles/Unlit``，出现上述问题

## 参考文档
[Render Texture Transparency not working anymore in URP](https://forum.unity.com/threads/render-texture-transparency-not-working-anymore-in-urp-coming-from-lwrp.749441/)




