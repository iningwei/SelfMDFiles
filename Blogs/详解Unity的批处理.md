## 渲染流水线
简言之渲染流水线的任务就是把三维场景渲染为二维图像。这个过程通常由CPU和GPU共同完成。
在渲染物体之前，CPU需要为渲染物体做很多准备工作，主要包括如下三个步骤：
- Data Transfer(数据加载到显存中)
程序开始之前，所有的数据都是在磁盘(HDD)中的。程序运行后，为了CPU能够快速访问数据，需要先把数据载入到内存(RAM)。GPU也有自己的可以快速访问的类内存结构，叫显存（VRAM），当需要渲染物体时，网格和纹理等数据又会被加载到显存中。
- Set Render States(设置渲染状态)
渲染状态(Render State)定义了场景中的网格应该如何被渲染。包括使用哪个shader、光源、材质、纹理等。在调用图形渲染API进行绘制前需要设置好这些状态值，它们能够指导GPU如何渲染我们传递到显存的数据。
- Draw Call(调用图形渲染API)
在模型和纹理等数据传输到显存，并设置了正确的渲染状态后，CPU调用渲染API函数通知GPU进行绘制，这个步骤叫做“Draw Call”。
CPU和GPU是并行工作的，它们之间通过命令缓冲区(Command Buffer)这个队列进行沟通，大概如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210712192947.png)

还有一个概念:批次(Batch)，也很重要。调用一次渲染API的绘制接口向GPU提交使用相同渲染状态来渲染物体的行为称为渲染批次。从渲染API的调用角度看，Batch和Draw Call是一样的。但是在游戏引擎中它们的实际意义是不一样的。Batch一般是指经过打包之后的Draw Call。如下两种缓冲区命令可以看出它们的区别：
``渲染状态A --> 渲染A -->渲染状态B --> 渲染B --> 渲染状态C --> 渲染C`` Draw Call：3 Batch：3

``渲染状态A --> 渲染A --> 渲染B --> 渲染C`` Draw Call: 3 Batch：1

从CPU到GPU最消耗性能的地方是切换渲染状态，而不是Draw Call这个操作。因此在Unity引擎中也越来越淡化Draw Call，转而使用Batchs作为Graphics的性能指标。

## Unity中批处理的类型
- Static Batching(静态批处理)
工作原理是为静态游戏对象奖励共享的顶点和索引缓冲区，绘制的时候根据Unity引擎渲染排序函数，将符合静态合批的对象，一次性提交整个合并模型的顶点数据，然后设置一次渲染状态，调用多次Draw Call分别绘制每一个子模型。
``优点：``对于有相同材质的静态物体，Unity能把相同渲染状态的对象通过一个批次添加到命令缓冲区供GPU去渲染，从而加速了渲染效率。
``缺点：``会增加运行时内存，会增加包体大小
（增加包体大小，是因为Build的时候，Unity会把符合静态合批的对象生成一个大的合并网格，因此会增加包体）
（增加运行时内存，是因为CPU加载的也是合并后的网格，合并后的网格越大，增加的内存也越大）

Unity还提供了一种灵活度比较高的运行时静态合批方法，通过调用StaticBatchingUtility.Combine可以在不设置Static标签的情况下把目标物体及其子物体进行合并。使用这种方法可以避免包体的增大。但是运行时合并需要消耗CPU进行运算，因此也会带来内存和CPU开销。
- Dynamic Batching(动态批处理)
引擎自动将符合条件(相同Mesh，相同材质等条件)的物体合并到一个Draw Call内绘制。为了合并渲染批次，动态批处理每一帧都会有一些CPU性能消耗，用于计算模型的顶点变换。
优点：弥补了静态批处理限定了物体不能有位移的要求
缺点：CPU性能消耗、条件苛刻

- GPU Instancing
静态批处理和动态批处理，都有自己的优势和劣势。但这些都是在CPU端做的优化，实际上GPU的运行速度是很高的，一次性渲染20个对象和一次性渲染200个对象，几乎是一样的。以上两种提升渲染能力的方法都是针对Command buffer，那我们能不能另辟蹊径，针对GPU来做个优化。比如：一个Draw Call，绘制一个对象；那能不能告诉GPU，一个Draw Call ，给我绘制一百个对象。GPU Instancing正是为了解决这个问题出现的。

补充：在Unity的ProjectSettings-->Player界面有统一的静态批处理、动态批处理开关。

## GPU Instancing

Unity 会将可见范围内的所有符合要求的 Object 的对象的属性（位置，uv等）放入到 GPU 中的缓冲区中，从中抽取一个对象作为实例送入渲染流程，这个对象包含了所有符合要求对象的公共信息（那些它们相同的部分），收到 Draw Call 之后，从显存中取出实例的部分共享信息与从GPU常量缓冲器中取出对应对象的相关信息一并传递到下一渲染阶段。

Unity5.4起增加了对GPU Instancing的支持。

## 支持GPU Instacing的平台

- DirectX 11+ on Windows
- OpenGL ES3.0+/OpenGL Core 4.1+ on Windows,macOS,Linux,iOS and Android
- Vulkan on Windows,Linux and android
- WebGL(requires WebGL 2.0 API)

### 如何使用GPU Instancing

 参见官方文档：

[GPU instancing](https://docs.unity3d.com/2019.4/Documentation/Manual/GPUInstancing.html)

[Creating shaders that support GPU instancing](https://docs.unity3d.com/2021.3/Documentation/Manual/gpu-instancing-shader.html)


## 合批失败原因汇总
Unity官方使用Unity5.6.0b6制作了一个集合了各种合批失败原因的工程：[BatchBreakingCause](https://github.com/Unity-Technologies/BatchBreakingCause)。笔者使用Unity5.6.7f1和Unity2020.3.2f1两个版本进行了对比测试。
（以下测试只针对Unity默认渲染管线,且测试场景中使用的物体都是Unity内置的物体，比如Cube,Plane,Capsule等）

- Additional Vertex Streams
Demo中是自带的两个Cube，使用的Standard Shader。按理说会自动的动态合批。但是在代码层使用了[MeshRenderer.additionalVertexStreams](https://docs.unity3d.com/2017.3/Documentation/ScriptReference/MeshRenderer-additionalVertexStreams.html)设定了额外的顶点信息流。
笔者在两个版本中Frame Debug给的合批失败的的提示内容却都是:The material doesn't have GPU instancing enabled。
扩展测试：设置Enable Instancing后，Instancing成功。
改成该工程中不支持GPU Instancing的Unlit/Color Shader后，提示信息为：A mesh renderer has additional vertex streams.Dynamic batching doesn't support such mesh renderers.
原因分析：GPU Instancing优先级高于动态批处理。由于使用的Shader默认是支持GPU Instancing的，但是Shader并没有开启Instancing，因此导致Instancing失败，所以提示信息才变成了The material doesn't have GPU instancing enabled。当使用不支持Instancing的Shader，当尝试进行批处理时，由于顶点信息流使动态批处理失败


- Deferred Objects on Different Lighting Layers
Demo中是两个相同的Cube，使用的Standard Shader，有不同的Layer设置，其中一个Cube的Layer接受了光照。造成动态合批失败。
设置Enable Instancing依旧无法合批。
两个Unity版本两种不同的操作的提示内容都是：Objects are on different lighting layers.

- Deferred Objects Split by Shadow Distance
Demo中是两个相同的Cube，使用的Standard Shader。其中一个在光照的阴影范围内，即在相机下是可以产生阴影的。另一个距离相机较远，不产生阴影。
在Unity5.6.7f1中动态批处理合批失败，提示为：Some objects are within the shadow distance,while some other objects are not。
在Unity2020.3.2f1中却可以动态批处理成功。
扩展测试：Unity2020.3.2f1工程中启动Instancing后，会执行Instancing合批，即Instancing合批优先级更高。但是在Unity5.6.7f1中Instancing合批也失败，提示信息和上述一样。

- Different Combined Meshes
Demo中有两个父物体，父物体下都有两个子物体：一个Cube和一个Sphere，使用的Standard Shader。父物体上调用了``StaticBatchingUtility.Combine(gameObject);``进行代码层的静态合批。两个被合批的物体无法进行批处理。
在Unity5.6.7f1中的提示信息为：The combined mesh is different between static-batched objects.
在Unity2020.3.2f1中的提示信息为：Objects belong to different static batches.


- Different Custom Properties
Demo中两个相同的Cube，使用的Standard Shader，代码层使用MaterialPropertyBlock为两个Cube设置了不同的颜色；导致动态批处理失败。两个版本的Unity提示信息均为：Objects have different Material PropertyBlock set.
这种情况下要合批可以考虑开启Instancing，同时要扩展Shader使支持颜色属性。

- Different Lights 
Demo中两个相同的Cube，使用的Standard Shader，被不同的光源影响。导致动态批处理失败。两个版本Unity的提示信息一致：Objects are affected by different forward lights。
扩展测试：GPU Instancing这种情况下也会失败。

- Different Materials
Demo中两个Cube，虽然Mesh网格一致，都使用的Standard Shader，但是使用了不同的材质球，导致动态批处理失败。提示信息均为：Objects have different matierials

- Different Reflection Probes 
Demo中两个相同Cube，使用的Standard Shader，两个反射探针分别罩着它们。动态批处理失败。
两个版本Unity提示信息均为：Objects are affected by different reflection probes.
经扩展测试，GPU Instancing这种情况下也会失败。


- Different Shadow Caster Hash
Demo场景中两个Cube，使用了不同的材质球，均使用了Stanard Shader的Cutout模式，但是Alobedo的颜色和Alpha Cutoff的值设置不同。另外场景中有Directional灯光，且对两个Cube均有影响。
由于使用的材质球不一样，因此在绘制两个Cube的时候无法动态合批。提示信息都是：Objects have different matierials
由于灯光的影响，会产生阴影，两个Material的shader参数设置不一致会影响阴影投射pass导致阴影这一块无法合批，两个版本Unity的提示信息都是：Objects either have different shadow caster shaders,or have different shader properties/keywords that affect the output of the shadow caster pass.
扩展测试把两个材质球的参数调整一致后，两个Cube的阴影实现了合批。

- Different Shadow Receiving Settings
Demo场景中两个Cube，使用的Standard Shader，唯一的区别是一个设置了接受阴影，一个设置了不接受阴影。导致动态合批失败。提示信息均为：Either objects have different "Receive Shadows" settings,or some objects are within the shadow distance,while some other objects are not.
扩展测试，GPU Instancing也不生效。

- Different Static Batching Flags 
Demo场景中三个相同Cube，使用的Standard Shader，其中两个设置了``Batching Static``,运行后，设置了静态批处理的两个Cube合批，未设置的无法和它们合批，且有提示信息：Objects have different batching-static settings.
扩展测试：若Enable GPU Instancing开启，那么两个设置了静态批处理的依旧会合批，未设置的无法合批，但是没有了提示信息。


- Dynamic Batching Disabled to Avoid Z-Fighting 
Demo场景中两个相同Cube，Shader使用的是Unlit/Color,相机的渲染路径选择了Deferred。两个版本中的合批提示都是：
Dynamic Batching is turned off in the Player Settings or is disabled temporarily in the current context to avoid z-fighting.
若渲染路径改成Forward,shader使用Unlit/Color，那么动态合批成功。
若渲染路径使用Deferred,shader使用standard，那么动态合批成功。
至于为什么Deferred渲染路径和Unlit/Color会导致Z-Fighting,目前笔者不得而知，希望知道的大佬不吝赐教？？？？？

- Instancing Different Geometries
Demo场景中两个Cube,两个Sphere，使用同一个材质球，四个物体都使用了自定义的支持GPUInstancing的Shader。且材质球上开启了GPU instancing。两个Cube之间合批成功，两个Sphere之间合批成功。它们彼此无法合批，因为Mesh不一致。提示信息为：Rendering different meshes or submeshes with GPU instancing.


- Lightmapped Objects
Demo场景中有三个Cube，使用的Standard Shader, 其中两个缩放一致，另一个比较高,其它设置一样。场景中的灯光使用的是Baked模式。
在Unity5.6.7f1中三个Cube都勾选了Lightmap Static。
在Unity2020.3.2f1中三个Cube勾选的是Contribute GI 和Reflection Probe Static。（这里不确定低版本往高版本转换后的设置对否）
由于光照贴图不兼容，因此在新版本Unity中，Lighting->Baked Lightmaps面板中重新生成了光照贴图。
三个Cube无法动态合批，提示信息为：Objects are lightmapped。
扩展测试，开启GPU Instancing也无法合批


- Lightprobe Affected Objects
Demo场景中有两个Cube，使用的Standard Shader...
这个案例没看懂#！

- Mixed Sided Mode Shadow Casters 
Demo场景中两个Quad，使用的Standard Shader，其中一个Cast Shadows设置为On，另一个设置为Two Sided。两个模型的渲染动态合批。但是阴影的渲染无法合批，提示信息为：Objects have different "Cast Shadows" settings.


- Multipass
Demo场景中两个相同Cube，使用自定义Shader，shader由3个pass组成。无法动态合批，提示信息：An object is using a multi-pass shader.

- Multiple Forward Lights
Demo场景中只有一个Cube，使用的是Standard shader，三个灯光，其中一个平行光，两个Spot光；相机Rendering Path为Forward。因此三个灯光都会运算一次，使得一个Cube被绘制3次。提示信息为：An object is affected by multiple forward lights.
扩展测试，开启GPU Instancing依旧会被绘制3次。


- Non-instanceable Property Set 
Demo场景中两个相同Cube，使用同一个Material，且勾选了Enable GPU Instancing。Shader如下：
```csharp
Shader "Example/Instancing" {
	Properties {
		_Color ("Color", Color) = (1,1,1,1)
		_MainTex ("Albedo (RGB)", 2D) = "white" {}
		_Glossiness ("Smoothness", Range(0,1)) = 0.5
		_Metallic ("Metallic", Range(0,1)) = 0.0
	}
	SubShader {
		Tags { "RenderType"="Opaque" }
		LOD 200
		
		CGPROGRAM
		// Physically based Standard lighting model, and enable shadows on all light types
		// And generate the shadow pass with instancing support
		#pragma surface surf Standard fullforwardshadows addshadow

		// Use shader model 3.0 target, to get nicer looking lighting
		#pragma target 3.0

		// Enable instancing for this shader
		#pragma multi_compile_instancing

		// Config maxcount. See manual page.
		// #pragma instancing_options

		sampler2D _MainTex;

		struct Input {
			float2 uv_MainTex;
		};

		half _Glossiness;
		half _Metallic;

		// Declare instanced properties inside a cbuffer.
		// Each instanced property is an array of by default 500(D3D)/128(GL) elements. Since D3D and GL imposes a certain limitation
		// of 64KB and 16KB respectively on the size of a cubffer, the default array size thus allows two matrix arrays in one cbuffer.
		// Use maxcount option on #pragma instancing_options directive to specify array size other than default (divided by 4 when used
		// for GL).
		UNITY_INSTANCING_BUFFER_START(Props)
		UNITY_DEFINE_INSTANCED_PROP(fixed4, _Color)	// Make _Color an instanced property (i.e. an array)
		UNITY_INSTANCING_BUFFER_END(Props)

		void surf (Input IN, inout SurfaceOutputStandard o) {
			// Albedo comes from a texture tinted by color
			fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * UNITY_ACCESS_INSTANCED_PROP(Props, _Color);
			o.Albedo = c.rgb;
			// Metallic and smoothness come from slider variables
			o.Metallic = _Metallic;
			o.Smoothness = _Glossiness;
			o.Alpha = c.a;
		}
		ENDCG
	}
	FallBack "Diffuse"
}
```
代码层通过MaterialPropertyBlock对它们分别设置不同的_Color和_Metallic属性。
由Shader代码可知，只对_Color属性进行了扩展，因此Intancing合批失败，提示信息为：Non-instanced properties set for instanced shader

- Odd Negative Scaling 
Demo场景中四个Cube，使用的Standard Shader，不同的是其中有一个Cube的Y方向缩放是负值。导致动态批处理失败，提示信息为：An object has odd negative scaling。
有负值缩放的Cube会打断原来的合批，再加上场景中各个Cube的位置原因，导致只有两个Cube完成了动态合批。
扩展测试：开启Instancing，结果是一样的，两个Cube合批，另外两个被打断合批，且提示信息和上面一致。

- Shader Disables Batching
Demo场景中两个相同Cube，使用的Shader如下：
```csharp
Shader "Example/DisableBatching"
{
	Properties
	{
		_MainTex ("Texture", 2D) = "white" {}
	}
	SubShader
	{
		Tags { "RenderType"="Opaque" "DisableBatching"="True" }
		LOD 100

		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			// make fog work
			#pragma multi_compile_fog
			
			#include "UnityCG.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float2 uv : TEXCOORD0;
			};

			struct v2f
			{
				float2 uv : TEXCOORD0;
				UNITY_FOG_COORDS(1)
				float4 vertex : SV_POSITION;
			};

			sampler2D _MainTex;
			float4 _MainTex_ST;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.uv = TRANSFORM_TEX(v.uv, _MainTex);
				UNITY_TRANSFER_FOG(o,o.vertex);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				// sample the texture
				fixed4 col = tex2D(_MainTex, i.uv);
				// apply fog
				UNITY_APPLY_FOG(i.fogCoord, col);
				return col;
			}
			ENDCG
		}
	}
}

```
可见shader中声明了``"DisableBatching"="True"``这个标签，导致合批失败，提示信息为：The shader explicitly disables batching with the "DisableBatching" tag.

- Too Many Indices in Dynamic Batch
Demo场景中很多相同的Plane，使用的Standard Shader,从FrameDebug可以看出应该是动态合为了两个批次，提示信息也是在第二个合批物体上提示其无法和上一个合批物体进行动态合批：
There are too many indices(more than 32k) in a dynamic batch.
扩展测试：启用GPU Instancing则可以合成一个批次

- Too Many Indices in Static Batch
Demo场景中很多Capsule，使用的Standard Shader，且勾选了Static。
在Unity5.6.7f1通过静态批处理最终合成了三个批次。无法合成一个批次的提示信息为：There are too many indices in the combined mesh of a static batch.The limit is 48k indices on OpenGLES,32k on macOS and 64k on other platforms。
在Unity2020.3.2f1通过静态批处理则可以合为一个批次。``新版本的静态批处理对indices的限制到底如何呢？``
扩展测试：开启GPU Instancing，在Unity5.6.7f1的表现和上述一样，进行了静态批处理。``能否得到结论静态批处理优先级大于Instancing？``
在Unity2020.3.2f1中编辑器处于非运行状态进行了Instancing，结果为一个批次。运行状态则进行了静态批处理，合批结果为一个批次。

- Too Many Vertex Attributes for Dynamic Batching
Demo场景中两个相同Plane,使用的Shader为：
```csharp
Shader "Example/ManyVertexAttributes"
{
	Properties
	{
		_MainTex ("Texture", 2D) = "white" {}
	}
	SubShader
	{
		Tags { "RenderType"="Opaque" }
		LOD 100

		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			// make fog work
			#pragma multi_compile_fog
			
			#include "UnityCG.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float2 uv : TEXCOORD0;
				float2 uv1 : TEXCOORD1;
				float2 uv2 : TEXCOORD2;
				float2 uv3 : TEXCOORD3;
				float2 uv4 : COLOR;
				float2 uv5 : NORMAL;
				float2 uv6 : TANGENT;
			};

			struct v2f
			{
				float2 uv : TEXCOORD0;
				UNITY_FOG_COORDS(1)
				float4 vertex : SV_POSITION;
			};

			sampler2D _MainTex;
			float4 _MainTex_ST;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.uv = TRANSFORM_TEX(v.uv + v.uv1 + v.uv2 + v.uv3 + v.uv4 + v.uv5 + v.uv6, _MainTex);
				UNITY_TRANSFER_FOG(o,o.vertex);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				// sample the texture
				fixed4 col = tex2D(_MainTex, i.uv);
				// apply fog
				UNITY_APPLY_FOG(i.fogCoord, col);
				return col;
			}
			ENDCG
		}
	}
}

```
动态合批失败，提示信息为：A submesh we are trying to dynamic-batch has more than 900 vertex attributes.
由于Unity内置Plane由很多网格组成，顶点数也非常多。加上该Shader定义了很多顶点属性。导致一个Plane进行渲染时总顶点属性超过了900个。

- Too Many Vertices for Dynamic Batching
Demo场景中两个相同的Sphere, 使用的Shader为Standard。动态合批失败，提示信息为：The material doesn't have GPU instancing enabled.
扩展测试：开启GPU Instancing，instancing合批成功
原因分析：由于GPU Instancing优先级高于动态批处理。使用的Shader支持Instancing，因此先尝试进行Instancing，但是由于未开启Instancing，因此有了上述提示信息。
为了验证这个猜想，笔者使用了工程中自带的Unlit/Color这个vf shader。其默认并不支持Instancing。果然提示信息变成动态批处理失败提示了：A submesh we are trying to dynamic-batch has more than 300 vertices。这是因为Unity中的默认Sphere顶点数很多，因此动态合批失败。

## 结论
通过以上测试笔者得到如下结论（只针对场景物体，UGUI另讨论）：
- 批处理优先级
``静态批处理 > GPU Instancing > 动态批处理``
在满足多个批处理的条件下，按照优先级进行，靠前的执行后（无论成功与否），不再执行后续的。比如物体使用了支持GPU Instancing的Shader，无论开启与否或者成功与否，后续都不再尝试动态批处理

- 动态批处理
物体需要有相同的Mesh、相同的材质，这是基础条件。
物体的旋转、``正缩放``不影响合批。
物体顶点数不能超过300个，顶点属性不能超过900个。否则无法参与合批。
一个动态批处理索引上限是32K，超过后会组装新的动态批次。
动态批处理会因为渲染顺序的缘故被打断。比如 ``较远距离有若干物体A、中等距离有若干物体B、近距离有若干物体A，``假设A、B使用相同材质，只是Mesh不一样，没有其它影响合批的因素。那么最终这些物体会被动态合批3次，中等距离的若干物体B，打断了物体A之间的合批。当删除物体B，所有的物体A实现了动态合批。



## 补充
- Objects are rendered using different rendering functions
