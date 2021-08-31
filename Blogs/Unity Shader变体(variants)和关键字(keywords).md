## 前言
在Unity Shader中通过控制关键字的开关可以实现让Shader对相同代码的进行复用。Unity编译后，会为不同的关键字组合生成不同的独立的Shader代码，即Shader变体。
Shader变体可以使得工作流更加简单和高效，比如你通过关键字的指定，从而实现为不同功能的材质球指定相同的Shader；通过使用更少的Shader资源来对工程进行管理；同时可以在运行时支持对关键字的开关和设置，从而实现对Shader行为的设置。

## Shader变体创建
通常使用如下``pragma指令``之一进行shader变体的创建：
- #pragma multi_compile
- #pragma multi_compile_local
- #pragma shader_feature
- #pragma shader_feature_local


## 代码层设置keyword
如下API可以对shader keywords进行开关，需要注意前提是这些变体已经顺利编译
- Shader.EnableKeyword:开启某个全局keyword
- Shader.DisableKeyword:关闭某个全局keyword
- CommandBuffer.EnableShaderKeyword:使用CommandBuffer，开启某个全局keyword
- CommandBuffer.DisableShaderKeyword:使用CommandBuffer，关闭某个全局keyword
- Material.EnableKeyword:对某个特定shader（该材质球引用的）开启local keyword
- Material.DisableKeyword:对某个特定shader（该材质球引用的）关闭local keyword
- ComputeShader.EnableKeyword:对某compute shader开启某local keyword
- ComputeShader.DisableKeyword:对某compute shader关闭某local keyword


## multi_compile是如何工作的
示例：
```csharp
#pragma multi_compile FANCY_STUFF_OFF FANCY_STUFF_ON
```
上述代码创建了两个shader变体，即FANCY_STUFF_OFF和FANCY_STUFF_ON，在运行的时候Unity会根据Material或者Global shader keyword来对其进行设置。如果代码中未指定，那么Unity会默认使用第一个（上述代码中即FANCY_STUFF_OFF）

你也可以使用一条multi_compile指令创建超过两个keyword,如：
```csharp
#pragma multi_compile SIMPLE_SHADING BETTER_SHADING GOOD_SHADING BEST_SHADING
```

在multi_compile命令中，为了减少关键字数量，可以省略对其中一个关键字的定义，使用两个下划线(__)来代替。如：
```csharp
#pragma multi_compile __ FOO_ON
```
上述指令定义了两个shader变体，一个是没有任何定义(__)，另一个是FOO_ON

## shader_feature和multi_compile的区别
由shader_feature定义的keyword，若没有在Material中被引用，那么最终打包是不会被编译到包体的。
由multi_compile定义的keyword，无论引用与否，在没有外力干预的情况下（下文会提到变体剥离相关内容）都会被编译打包到最终包体。

因此shader_feature指令支持一个keyword，如：``#pragma shader_feature FANCY_STUFF``，其实它是`` #pragma shader_feature _ FANCY_STUFF``指令的缩写形式。

## keyword限制
Unity中全局keyword上限限制为384个（其中有大约60个是unity预留供内部使用的）；某个shader的本地keyword上限为64个

### multi_compile_local和shader_feature_local
这两个指令可以为某个shader单独定义keyword，避免全局keyword超过上限，需要注意的是shader的本地keyword上限为64个。

如果存在全局keyword和本地keyword同名的情况，unity会优先使用本地keyword


## keyword的阶段特异性指令
默认情况下Unity会为每一个shader变体生成其每个阶段的shader代码，虽然在最终打包时引擎会自动移除重复代码，但是依旧存在shader编译时的时间浪费，因此可以为变体创建指令增加后缀，来指定其只在某个阶段生成代码。
可用的后缀包括``_vertex``, ``_fragment``, ``_hull``, ``_domain``, ``_geometry``,``_raytracing``。其最终命令如：``shader_feature_local_vertex``。也可以对同一个keyword使用多个阶段特异性指令实现对其多个阶段的控制。

## 内置multi_compile
在Built-in渲染管线中，有一些用来处理光照、阴影、光照贴图、雾效的指令：
- multi_compile_fwdbase:编译正向基础渲染通道(用于正向渲染中，应用环境光照、主方向光照和顶点/球面调和光照(Spherical Harmonic Lighting))所需的所有变体。这些变体用于处理不同的光照贴图类型、主要方向光源的阴影选项的开关与否。
- multi_compile_fwdadd:编译正向附加渲染通道(用于正向渲染中；以每个光照一个通道的方式应用附加的逐像素光照)所需的所有变体。这些变体用于处理光源的类型(方向光源、聚光灯或者点光源)，且这些变体都包含纹理cookie。
- multi_compile_fwdadd_fullshadows:在multi_compile_fwdadd的基础上增加了对光照实时阴影的支持
- multi_compile_fog:包含了对雾效处理的变体（off/linear/exp/exp2）

这些内置指令都包括较多的变体，对于那些确定不需要的变体，可以使用``#pragma skip_variants``指令移除，比如：
```csharp
#pragma multi_compile_fwdadd
#pragma skip_variants POINT POINT_COOKIE
```
即从multi_compile_fwdadd指令涉及到的变体中移除包含POINT或者POINT_COOKIE的

## shader变体剥离
对于确定不需要的shader变体是不必要打入包体的，剥离不必要的变体同样可以降低打包时间和包体大小。通过对以下回调监听，可以自主控制shader变体打包：
- [IPreprocessShaders.OnProcessShader](https://docs.unity3d.com/ScriptReference/Build.IPreprocessShaders.OnProcessShader.html) :在Unity编译普通Shader前会收到该回调
- IPreprocessComputeShaders.OnProcessComputeShader:在Unity编译Compute Shader前会收到该回调

更多参考：[Stripping scriptable shader variants](https://blog.unity.com/technology/stripping-scriptable-shader-variants)

## 参考文档
[Shader variants and keywords](https://docs.unity3d.com/Manual/SL-MultipleProgramVariants.html)