## 什么是GPU Instancing
GPU Instancing可以使用较少的dc，一次性处理很多相同的mesh。这对于那些场景中重复出现的大量物体渲染是很有帮助的，比如树木、草、建筑等，GPU Instancing通过合批大大降低dc。

## 让材质球支持GPU Instancing
GPU Instancing是在shader层实现的，目前内置的Standard、StandardSpecular和所有的Surface Shader都默认支持。依赖这些Shader的材质球，在Inspector面板有Enable Instancing选项。勾选后即可支持。
但是这些默认的Shader只支持Transform级的GPUInstancing，如果两个相同的物体，使用同一个shader，在代码层通过MaterialPropertyBlock设置了不同的Color，即使开启了Enable Instancing选项，那么依旧是不能合批的（通过FrameDebug可以看到提示``None-instanced properties set for instanced shader``）。若想合批，只能对Shader扩展，使其支持新的数据。

URP是默认支持SRP Batcher的，且SRP Batcher优先级大于GPU Instancing，只有在关闭项目的SRP Batcher或者强制使用Graphics.DrawMeshInstanced绘制时才会使用GPU Instacing。详见：[Scriptable Render Pipeline Batcher](https://docs.unity.cn/2021.3/Documentation/Manual/SRPBatcher.html)

## 扩展Shader使其支持更多的材质球属性
[详见官方文档](https://docs.unity.cn/2021.3/Documentation/Manual/gpu-instancing-shader.html)
### Surface Shader
```c
Shader "Custom/InstancedColorSurfaceShader" {
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
        #pragma surface surf Standard fullforwardshadows
        // Use Shader model 3.0 target
        #pragma target 3.0
        sampler2D _MainTex;
        struct Input {
            float2 uv_MainTex;
        };
        half _Glossiness;
        half _Metallic;
        UNITY_INSTANCING_BUFFER_START(Props)
           UNITY_DEFINE_INSTANCED_PROP(fixed4, _Color)
        UNITY_INSTANCING_BUFFER_END(Props)
        void surf (Input IN, inout SurfaceOutputStandard o) {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * UNITY_ACCESS_INSTANCED_PROP(Props, _Color);
            o.Albedo = c.rgb;
            o.Metallic = _Metallic;
            o.Smoothness = _Glossiness;
            o.Alpha = c.a;
        }
        ENDCG
    }
    FallBack "Diffuse"
}
```
如上，我们定义了INSTANCED属性_Color，那么当我们使用MaterialPropertyBlock对物体设置该属性的时候，Unity会监听到，并把他们放到一个dc中处理。
### VF Shader
```c
Shader "SimplestInstancedShader"
{
    Properties
    {
        _Color ("Color", Color) = (1, 1, 1, 1)
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
            #pragma multi_compile_instancing //Generates instacing variants.This is required for vf shaders,it is optional for surface shaders
            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                UNITY_VERTEX_INPUT_INSTANCE_ID  //add instanceID to appdata
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                UNITY_VERTEX_INPUT_INSTANCE_ID // necessary only if you want to access instanced properties in fragment Shader.
            };

            UNITY_INSTANCING_BUFFER_START(Props)
                UNITY_DEFINE_INSTANCED_PROP(float4, _Color)
            UNITY_INSTANCING_BUFFER_END(Props)

            v2f vert(appdata v)
            {
                v2f o;

                UNITY_SETUP_INSTANCE_ID(v);  //
                UNITY_TRANSFER_INSTANCE_ID(v, o); // necessary only if you want to access instanced properties in the fragment Shader.

                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            fixed4 frag(v2f i) : SV_Target
            {
                UNITY_SETUP_INSTANCE_ID(i); // necessary only if any instanced properties are going to be accessed in the fragment Shader.
                return UNITY_ACCESS_INSTANCED_PROP(Props, _Color);
            }
            ENDCG
        }
    }
}
```
VF Shader添加INSTANCED属性要复杂一些。
首先要引入``#pragma multi_compile_instancing``，告诉Unity生成instancing变体(但是Surface Shader并不需要引入)。这样的话在Inspector面板才有Enable Instancing选项。

还需要了解下面几个关键宏：
UNITY_TRANSFER_INSTANCE_ID(v, o)：当需要在在片段着色器中访问每个 Instance 独有的属性时，用于在顶点着色器中将 Instance ID 从输入结构拷贝至输出结构中
UNITY_SETUP_INSTANCE_ID(v)：目的是让 Instance ID 在 Shader 函数里也能够被访问到，并且重载正确的矩阵数据（通过内部的 UnitySetupCompoundMatrices() 方法），需要在着色器的最前面调用
UNITY_INSTANCING_BUFFER_START(name) / UNITY_INSTANCING_BUFFER_END(name)：用于定义 Constant Buffer，每个 Instance 独有的属性必须定义在一个遵循特殊命名规则的 Constant Buffer 中
UNITY_DEFINE_INSTANCED_PROP(type, name)：第一个参数为属性类型，第二个参数为属性名字，该宏会定义一个 Uniform 数组

UNITY_ACCESS_INSTANCED_PROP(bufferName, name)：第一个参数为属性所在缓冲区名字，第二个参数为属性名字，该宏会使用 Instance ID 作为索引到 Uniform 数组中去取当前Instance 对应的数据
 

## GPU Instancing的限制
- 1，同mesh，同material,同shader是GPU Instancing的基础条件
- 2，不直接支持图片类型

比如相同材质球不同贴图的两个物体，是无法进行GPU Instancing的。
若尝试修改Shader为其增加对目标贴图属性进行支持后，该shader也会报错。
一般来说只支持数值类型的属性。
解决方法参见：https://answer.uwa4d.com/question/5b1e22f454646026e9ddd77d
- 3，GPU Instancing一次合批受限于GPU内存缓冲区的容量限制。

一般而言台式机（D3D）的常量缓冲区（constant buffers）大小为64KB，大多数移动设备(OpenGL)最大容量为16KB。
因此对于很多相同的物体，比如几千上万个cube，GPU Instancing也无法在一个批次中完成渲染。

上述常量缓冲区大小限制的信息只在[Unity5.5版本的官方文档](https://docs.unity3d.com/550/Documentation/Manual/GPUInstancing.html) 中提及。

常量缓冲区的存在也导致一次合批是有数量限制的，在Unity编辑器下的Frame Debug中可以查看到这个每次合批的数量限制通常是511个。

至于在WindowsPC下这个值为什么是511，上文也提到了默认的支持GPUInstancing的Shader只支持transform级的合批。在底层shader文件中CBUFFER_START与CBUFFER_END之间定义了int unity_BaseInstanceID 以及在UNITY_INSTANCING_BUFFER_START和UNITY_INSTANCING_CBUFFER_END之间定义了两个float4X4： unity_ObjectToWorldArray和unity_WorldToObjectArray。
```c
以上两个矩阵，每个矩阵使用4*(float的size)*4(四行)*4（四列） = 64byte 。则两个矩阵为128byte。

constant Buffer 为64KB = 65536 byte

65536btte/128byte = 512个对象
```
但一个Unity_BaseInstanceID为4个byte, 所以512个对象少一个刚好511。

以上是为什么显示511个对象的问题，其实在OpenGL上constant Buff只是16K， 这样推理少了4倍那手机应该为127个对象。

## 支持平台
- DirectX 11 and DirectX 12 on Windows

- OpenGL Core 4.1+/ES3.0+ on Windows, macOS, Linux, and Android

- Metal on macOS and iOS

- Vulkan on Windows, Linux and Android

- PlayStation 4 and Xbox One

- WebGL(requires WebGL 2.0 API)



## 参考文档
[官方文档](https://docs.unity3d.com/Manual/GPUInstancing.html)



