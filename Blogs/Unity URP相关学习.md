URP HLSL shader模版

https://blog.csdn.net/lvcoc/article/details/110751548

https://pastebin.com/E3tmPZis

https://pastebin.com/6QUAGPYZ

https://pastebin.com/kC9M0CzH

https://gist.github.com/phi-lira/225cd7c5e8545be602dca4eb5ed111ba 




## [Unity]URP HLSL Shader自用模板
https://www.cnblogs.com/lofu/p/17610002.html
```c
Shader "URP/falushan"
{
    Properties //着色器的输入 
    {
        _BaseMap ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        Tags {
            "RenderType"="Opaque"
            "RenderPipeLine"="UniversalRenderPipeline" //用于指明使用URP来渲染
        }

        HLSLINCLUDE 
        #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"
        #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl" 
        #include "Packages/com.unity.render-pipelines.core/ShaderLibrary/UnityInstancing.hlsl" 
        #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/DeclareDepthTexture.hlsl"

        CBUFFER_START(UnityPerMaterial) //声明变量
            float4 _BaseMap_ST;
        CBUFFER_END

        TEXTURE2D(_BaseMap); //贴图采样  
        SAMPLER(sampler_BaseMap);

        struct a2v //顶点着色器
        {
            float4 positionOS: POSITION;
            float3 normalOS: TANGENT;
            half4 vertexColor: COLOR;
            float2 uv : TEXCOORD0;
        };

        struct v2f //片元着色器
        {
            float4 positionCS: SV_POSITION;
            float2 uv: TEXCOORD0;
            half4 vertexColor: COLOR;
        }; 

        ENDHLSL

        Pass
        {
            HLSLPROGRAM
            #pragma vertex vert
            #pragma fragment frag


            v2f vert (a2v v)
            {
                v2f o;
                o.positionCS = TransformObjectToHClip(v.positionOS);
                o.uv = TRANSFORM_TEX(v.uv, _BaseMap);
                o.vertexColor = v.vertexColor;
                return o;
            }

            half4 frag (v2f i) : SV_Target  /* 注意在HLSL中，fixed4类型变成了half4类型*/
            {
                half4 col = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, i.uv);
                half res=lerp(i.vertexColor,col, i.vertexColor.g);
                return half4(res,res,res,1.0);
            }
            ENDHLSL
        }
    }
}
```
