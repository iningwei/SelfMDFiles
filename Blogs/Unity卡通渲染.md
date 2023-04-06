## 概述
[Unity卡通渲染总结 NPR Toon Shading](https://zhuanlan.zhihu.com/p/330599077)

## 商店资源
[OmniShade Pro移动优化着色器](https://assetstore.unity.com/packages/vfx/shaders/omnishade-pro-mobile-optimized-shader-213594)
[Flat Kit: Toon Shading and Water](https://assetstore.unity.com/packages/vfx/shaders/flat-kit-toon-shading-and-water-143368)
[MK Toon - Stylized Shader](https://assetstore.unity.com/packages/vfx/shaders/mk-toon-stylized-shader-178415)
[Toony Colors Pro 2](https://assetstore.unity.com/packages/vfx/shaders/toony-colors-pro-2-8105)

## git开源
[UnityChanToonShaderVer2_Project](https://github.com/unity3d-jp/UnityChanToonShaderVer2_Project)

## 军团要塞2卡通渲染算法实现
原文：http://richbabe.top/2018/12/12/%E5%86%9B%E5%9B%A2%E8%A6%81%E5%A1%9E2-%E5%8D%A1%E9%80%9A%E6%B8%B2%E6%9F%93%E7%AE%97%E6%B3%95%E5%AE%9E%E7%8E%B0/

shader如下：
```c
Shader "NPR_Lab/TeamFotress2" {
	Properties {
		_Color("Color", Color) = (1,1,1,1)
		_MainTex ("Albedo (RGB)", 2D) = "white" {}
		_RampTex ("Ramp Texture", 2D) = "white" {}
		_DiffuseCubeMap("Diffuse Convolution Cubemap", Cube) = ""{}
		_Amount("Diffuse Amount", Range(-10,10)) = 1

		_SpecularMask("Specular Mask", 2D) = "white" {}
		_Specular("Speculr Exponent", Range(0.1, 128)) = 128
		_RimMask("Rim Mask", 2D) = "white" {}
		_Rim("Rim Exponent", Range(0.1, 8)) = 1
		
		_SpecularColor("Specular Color", Color) = (1,1,1,1)
		_SpecularPower("Specular Power", Range(0, 30)) = 1
		_SpecularFresnel("Specular Fresnel Value", Range(0, 1)) = 0.28
		
		_RimColor("Rim Color", Color) = (0.26,0.19,0.16,0.0)
		_RimPower("Rim Power", Range(0.5,8.0)) = 3.0
	}
	SubShader {
		Pass{
			Tags{ "LightMode" = "ForwardBase" }

			CGPROGRAM

			#pragma vertex vert
			#pragma fragment frag

			#pragma multi_compile_fwdbase

			#include "UnityCG.cginc"
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			#include "UnityShaderVariables.cginc"

			//定义Properties变量
			fixed4 _Color;
			sampler2D _MainTex;//主纹理贴图
			float4 _MainTex_ST;
			sampler2D _RampTex;//漫反射变形贴图，通过查找映射创建一个硬阴影
			float4 _RampTex_ST;
			samplerCUBE _DiffuseCubeMap;
			float _Amount;

			sampler2D _SpecularMask;//高光遮罩
			float4 _SpecularMask_ST;
			float _Specular;
			sampler2D _RimMask;//边缘遮罩
			float4 _RimMask_ST;
			float _Rim;
			
			fixed4 _SpecularColor;
			float _SpecularPower;
			float _SpecularFresnel;
			fixed4 _RimColor;
			float _RimPower;
			
			//定义顶点着色器输入
			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float2 texcoord : TEXCOORD0;
				float4 tangent : TANGENT;
			};

			//定义顶点着色器输出
			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
				float2 mainTex_uv : TEXCOORD2;
				float2 rampTex_uv : TEXCOORD3;
				float2 specularMask_uv : TEXCOORD4;
				float2 rimMask_uv : TEXCOORD5;
				float3 up : TEXCOORD6;
			};

			//顶点着色器
			v2f vert(a2v v) {
				v2f o;

				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				o.up = mul(unity_ObjectToWorld, half4(0, 1, 0, 0)).xyz;

				o.mainTex_uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				o.rampTex_uv = TRANSFORM_TEX(v.texcoord, _RampTex);
				o.specularMask_uv = TRANSFORM_TEX(v.texcoord, _SpecularMask);
				o.rimMask_uv = TRANSFORM_TEX(v.texcoord, _RimMask);

				return o;
			}

			//片段着色器
			fixed4 frag(v2f i) : SV_Target{
				//计算所需的方向向量
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				fixed3 worldViewDir = normalize(UnityWorldSpaceViewDir(i.worldPos));
				fixed3 worldUp = normalize(i.up);

				//***计算非视角相关部分***
				//计算漫反射系数kd
				fixed4 c = tex2D(_MainTex, i.mainTex_uv) * _Color;
			    fixed3 kd = c.rgb;//albedo
				//计算Wraped diffuse term
				half difLight = dot(worldNormal, worldLightDir);//n·l
				half halfLambert = pow(0.5 * difLight + 0.5, 1.0);//半兰伯特因子
				half3 ramp = tex2D(_RampTex, float2(halfLambert, halfLambert)).rgb;//漫反射变形
				half3 difWarping = ramp * 2;//乘2使得模型更加明亮
				half3 wrapDiffuseTerm = _LightColor0.rgb * difWarping;
				//计算Ambient cube term
				float3 ambientCubeTerm = texCUBE(_DiffuseCubeMap, worldNormal).rgb * _Amount;
				//计算View Independent Lighting Terms
				half3 viewIndependentLightTerms = kd * (ambientCubeTerm + wrapDiffuseTerm);

				
				//***计算视角相关部分***
				//计算多重Phong高光部分
				/*
				half3 r = reflect(worldLightDir, worldNormal);//反射向量
				half3 refl = dot(r, worldViewDir);//r·v
				half fresnelForSpecular = 1;//fs，这里随便取了个值
				half fresnelForRim = pow(1 - dot(worldNormal, worldViewDir), 4);//fr = (1-(n·v))^4
				half3 ks = tex2D(_SpecularMask, i.specularMask_uv).rgb;//高光遮罩系数，如果为1应用高光，如果为0不应用
				half3 kr = tex2D(_RimMask, i.rimMask_uv).rgb;//边缘遮罩纹理值，用于减弱边缘高光对模型上某些部分的影响
				half3 multiplePhongTerms = _LightColor0.rgb * ks * max(fresnelForSpecular * pow(refl, _Specular), fresnelForRim * pow(refl, _Rim));
				*/
				float3 halfVector = normalize(worldLightDir + worldViewDir);//使用Blinn-Phong
				float3 specBase = pow(saturate(dot(halfVector, worldNormal)), _SpecularPower);
				float fresnel = 1.0 - dot(worldViewDir, halfVector);
				fresnel = pow(fresnel, 5.0);
				fresnel += _SpecularFresnel * (1.0 - fresnel);
				half3 multiplePhongTerms = specBase * fresnel * _LightColor0.rgb;

				//计算Rim Lighting部分
				/*
				half av = float(1);//对环境盒子的取值函数，这里省略直接取1
				half3 dedicatedRimLighting = dot(worldNormal, worldUp) * fresnelForRim * kr * av;
				*/
				half rim = 1.0 - saturate(dot(worldViewDir, worldNormal));
				half3 dedicatedRimLighting = _RimColor.rgb * pow(rim, _RimPower);
				half3 viewDependentLightTerms = multiplePhongTerms + dedicatedRimLighting;

				//最终输出颜色
				fixed3 finalCol = viewIndependentLightTerms + viewDependentLightTerms;

				return fixed4(finalCol, 1.0);
			}

			ENDCG
		}
	}
	FallBack "Diffuse"
}

```

扩展，使支持法线贴图
```c
Shader "NPR_Lab/TeamFotress2" {
	Properties {
		_Color("Color", Color) = (1,1,1,1)
		_MainTex ("Albedo (RGB)", 2D) = "white" {}
		[NoScaleOffset]_BumpMap ("Normal Map", 2D) = "bump" {}
		_BumpScale ("Bump Scale", float) = 1.0
		_RampTex ("Ramp Texture", 2D) = "white" {}
		_DiffuseCubeMap("Diffuse Convolution Cubemap", Cube) = ""{}
		_Amount("Diffuse Amount", Range(-10,10)) = 1

		_SpecularMask("Specular Mask", 2D) = "white" {}
		_Specular("Speculr Exponent", Range(0.1, 128)) = 128
		_RimMask("Rim Mask", 2D) = "white" {}
		_Rim("Rim Exponent", Range(0.1, 8)) = 1
		
		_SpecularColor("Specular Color", Color) = (1,1,1,1)
		_SpecularPower("Specular Power", Range(0, 30)) = 1
		_SpecularFresnel("Specular Fresnel Value", Range(0, 1)) = 0.28
		
		_RimColor("Rim Color", Color) = (0.26,0.19,0.16,0.0)
		_RimPower("Rim Power", Range(0.5,8.0)) = 3.0
	}
	SubShader {
		Pass{
			Tags{ "LightMode" = "ForwardBase" }

			CGPROGRAM

			#pragma vertex vert
			#pragma fragment frag

			#pragma multi_compile_fwdbase

			#include "UnityCG.cginc"
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			#include "UnityShaderVariables.cginc"

			//定义Properties变量
			fixed4 _Color;
			sampler2D _MainTex;//主纹理贴图
			float4 _MainTex_ST;
			sampler2D _BumpMap;
            float4 _BumpMap_ST;
			float _BumpScale;
			
			sampler2D _RampTex;//漫反射变形贴图，通过查找映射创建一个硬阴影
			float4 _RampTex_ST;
			samplerCUBE _DiffuseCubeMap;
			float _Amount;

			sampler2D _SpecularMask;//高光遮罩
			float4 _SpecularMask_ST;
			float _Specular;
			sampler2D _RimMask;//边缘遮罩
			float4 _RimMask_ST;
			float _Rim;
			
			fixed4 _SpecularColor;
			float _SpecularPower;
			float _SpecularFresnel;
			fixed4 _RimColor;
			float _RimPower;
			
			//定义顶点着色器输入
			struct appdata {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float2 texcoord : TEXCOORD0;
				float4 tangent : TANGENT;
			};

			//定义顶点着色器输出
			struct v2f {
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;
				
				float3 worldVertex : TEXCOORD1;
				float3 worldNormal : TEXCOORD2;
				float3 worldTangent : TEXCOORD3;
            	float3 worldBiTangent : TEXCOORD4;
				
				fixed4 t2w_0 : TEXCOORD5;
                fixed4 t2w_1 : TEXCOORD6;
                fixed4 t2w_2 : TEXCOORD7;
				
				float2 rampTex_uv : TEXCOORD8;
				float2 specularMask_uv : TEXCOORD9;
				float2 rimMask_uv : TEXCOORD10;
				float3 up : TEXCOORD11;
			};

			//顶点着色器
			v2f vert(appdata v) {
				v2f o;

				o.pos = UnityObjectToClipPos(v.vertex);
				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				
				fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);
				fixed4 worldVertex = mul(unity_ObjectToWorld, v.vertex);
				o.up = mul(unity_ObjectToWorld, half4(0, 1, 0, 0)).xyz;

				
				o.rampTex_uv = TRANSFORM_TEX(v.texcoord, _RampTex);
				o.specularMask_uv = TRANSFORM_TEX(v.texcoord, _SpecularMask);
				o.rimMask_uv = TRANSFORM_TEX(v.texcoord, _RimMask);


				fixed3 worldTangent = UnityObjectToWorldDir(v.tangent); 
                fixed3 worldBiTangent = cross(worldNormal,worldTangent) * v.tangent.w;
				
				  
                o.t2w_0 = float4(worldTangent.x,worldBiTangent.x,worldNormal.x, worldVertex.x);
                o.t2w_1 = float4(worldTangent.y,worldBiTangent.y,worldNormal.y, worldVertex.y);
                o.t2w_2 = float4(worldTangent.z,worldBiTangent.z,worldNormal.z, worldVertex.z);
				
				o.worldVertex =worldVertex;
				o.worldNormal =worldNormal;
				o.worldTangent = worldTangent;
				o.worldBiTangent = worldBiTangent;


				return o;
			}

			//片段着色器
			fixed4 frag(v2f i) : SV_Target{
				//计算所需的方向向量 
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldVertex));
				fixed3 worldViewDir = normalize(UnityWorldSpaceViewDir(i.worldVertex));
				fixed3 worldUp = normalize(i.up);
				
				/*-----------------------------------法线计算---------------------------------*/
                //获取  切线空间到世界空间的转换矩阵
                fixed3x3 tangentToWorld = fixed3x3(i.t2w_0.xyz, i.t2w_1.xyz, i.t2w_2.xyz);
                //采样切线空间法线贴图
                fixed4 bump = tex2D(_BumpMap, i.uv);
                //获取切线空间法线
                fixed3 tangentSpeaceNormal = UnpackNormalWithScale(bump,_BumpScale);
                //切线空间法线 转换到 世界空间
                fixed3 worldNormal = normalize(mul(tangentToWorld, tangentSpeaceNormal)); 
				
				
				

				//***计算非视角相关部分***
				//计算漫反射系数kd
				fixed4 c = tex2D(_MainTex, i.uv) * _Color;
				c=    c* saturate(dot(worldNormal, worldLightDir));
				
			    fixed3 kd = c.rgb;//albedo
				//计算Wraped diffuse term
				half difLight = dot(worldNormal, worldLightDir);//n·l
				half halfLambert = pow(0.5 * difLight + 0.5, 1.0);//半兰伯特因子
				half3 ramp = tex2D(_RampTex, float2(halfLambert, halfLambert)).rgb;//漫反射变形
				half3 difWarping = ramp * 2;//乘2使得模型更加明亮
				half3 wrapDiffuseTerm = _LightColor0.rgb * difWarping;
				//计算Ambient cube term
				float3 ambientCubeTerm = texCUBE(_DiffuseCubeMap, worldNormal).rgb * _Amount;
				//计算View Independent Lighting Terms
				half3 viewIndependentLightTerms = kd * (ambientCubeTerm + wrapDiffuseTerm);

				
				//***计算视角相关部分***
				//计算多重Phong高光部分
				/*
				half3 r = reflect(worldLightDir, worldNormal);//反射向量
				half3 refl = dot(r, worldViewDir);//r·v
				half fresnelForSpecular = 1;//fs，这里随便取了个值
				half fresnelForRim = pow(1 - dot(worldNormal, worldViewDir), 4);//fr = (1-(n·v))^4
				half3 ks = tex2D(_SpecularMask, i.specularMask_uv).rgb;//高光遮罩系数，如果为1应用高光，如果为0不应用
				half3 kr = tex2D(_RimMask, i.rimMask_uv).rgb;//边缘遮罩纹理值，用于减弱边缘高光对模型上某些部分的影响
				half3 multiplePhongTerms = _LightColor0.rgb * ks * max(fresnelForSpecular * pow(refl, _Specular), fresnelForRim * pow(refl, _Rim));
				*/
				float3 halfVector = normalize(worldLightDir + worldViewDir);//使用Blinn-Phong
				float3 specBase = pow(saturate(dot(halfVector, worldNormal)), _SpecularPower);
				float fresnel = 1.0 - dot(worldViewDir, halfVector);
				fresnel = pow(fresnel, 5.0);
				fresnel += _SpecularFresnel * (1.0 - fresnel);
				half3 multiplePhongTerms = specBase * fresnel * _LightColor0.rgb;

				//计算Rim Lighting部分
				/*
				half av = float(1);//对环境盒子的取值函数，这里省略直接取1
				half3 dedicatedRimLighting = dot(worldNormal, worldUp) * fresnelForRim * kr * av;
				*/
				half rim = 1.0 - saturate(dot(worldViewDir, worldNormal));
				half3 dedicatedRimLighting = _RimColor.rgb * pow(rim, _RimPower);
				half3 viewDependentLightTerms = multiplePhongTerms + dedicatedRimLighting;

				//最终输出颜色
				fixed3 finalCol = viewIndependentLightTerms + viewDependentLightTerms;

				return fixed4(finalCol, 1.0);
			}

			ENDCG
		}
	}
	FallBack "Diffuse"
}

```