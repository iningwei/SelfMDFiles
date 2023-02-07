## 前言
UGUI中使用RawImage加载RenderTexture是一种很常用的3D转2D的方案，常被用于角色立绘显示、特效显示等需求中。
## 流程
一般而言会有一个专门的相机对目标物体进行渲染，并把结果存储到RT(RenderTexture)中，然后在最终的RawImage中显示RT。
因此流程可以划分为两个阶段：
- Model $\rightarrow$ RT
这阶段Model使用的Shader是不固定的，千奇百怪。
- RT $\rightarrow$ RawImage
这阶段默认情况下RawImage使用的是``UI/Default``，由于这里只能给RawImage设置材质球，因此这里的Shader可控，可以自定义拓展。

通常会希望RT的背景是透明的，这也是符合大众审美的。因此渲染RT的相机通常会设置ClearFlags为SolidColor，且设置Background为(0,0,0,0)
## 遇到的问题
对于目标Model若其有使用Queue为Transparent或者AlphaTest的Surface Shader，那么从RT $\rightarrow$ RawImage的时候会发现使用这些Shader的模型部件(设为部件X)不可见。通过调整渲染RT的相机的Background的Alpha值虽然可以使这部分可见，但是也带来了不美观的背景。
笔者经过调试发现问题其实出在Model $\rightarrow$ RT 这个阶段，此阶段生成的RT虽然在Inspector面板内RGB模式下是视觉正常的，但是切换到A模式，会发现其Alpha通道已经丢失了上述提到的部件X的Alpha信息（这部分为什么会丢失，笔者暂时没有找到涉及到这一块的相关文档，还望知道的大佬不吝赐教）。导致后续通过RT $\rightarrow$ RawImage 部件X彻底不可见

## 解决方案
笔者目前针对这种情况总结出了两种解决方案：
1，使用两个相机，两个RT来渲染Model，通过这种方式可以算出目标Model的Alpha值，RT $\rightarrow$ RawImage的时候使用一个自定义的Shader，并用这个Alpha值来渲染。注意最终用来承载RT的RawImage需要设置Color为(0,0,0,1)或(1,1,1,1)。这种方式不要求限定渲染RT的原始相机的Background为(0,0,0,0)，可以为任意颜色。

2，在AnyPortrait 2D动画插件的官方文档[Rendering to Render Texture](https://rainyrizzle.github.io/en/AdvancedManual/AD_RenderTexture.html)中也提到了我上述提到的问题，其已经提供解决方案，通过扒代码，发现其是在Surface Shader中的#pragma中加入了keepalpha关键词。

比如Transparent/Diffuse的原始#pragma声明为：``#pragma surface surf Lambert alpha:fade``,调整为``#pragma surface surf Lambert keepalpha finalcolor:ApplyForwardBlendMode``, 并在Shader内添加函数ApplyForwardBlendMode
```c
		//KeepAlpha - ForwardBlendMode
		void ApplyForwardBlendMode(Input IN, SurfaceOutput o, inout half4 color)
		{
#ifdef UNITY_PASS_FORWARDADD
			color.rgb *= color.a;
#endif
		}
```

同理Transparent/Cutout/Diffuse的#pragma surface surf Lambert alphatest:_Cutoff, 可以调整为#pragma surface surf Lambert keepalpha finalcolor:ApplyForwardBlendMode。


