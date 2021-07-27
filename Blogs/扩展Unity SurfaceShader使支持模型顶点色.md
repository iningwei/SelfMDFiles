默认情况下Unity内置的SurfaceShader是不支持模型的顶点色的。通过简单的扩展可以使之对顶点色进行支持，下面以内置的``Legacy Shaders/Transparent/Cutout/Diffuse``为例说明。

## 原始shader内容
```c
// Unity built-in shader source. Copyright (c) 2016 Unity Technologies. MIT license (see license.txt)

Shader "My/Legacy Shaders/Transparent/Cutout/Diffuse" {
Properties {
    _Color ("Main Color", Color) = (1,1,1,1)
    _MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
    _Cutoff ("Alpha cutoff", Range(0,1)) = 0.5
}

SubShader {
    Tags {"Queue"="AlphaTest" "IgnoreProjector"="True" "RenderType"="TransparentCutout"}
    LOD 200

CGPROGRAM
#pragma surface surf Lambert alphatest:_Cutoff

sampler2D _MainTex;
fixed4 _Color;

struct Input {
    float2 uv_MainTex;
};

void surf (Input IN, inout SurfaceOutput o) {
    fixed4 c = tex2D(_MainTex, IN.uv_MainTex) * _Color;
    o.Albedo = c.rgb;
    o.Alpha = c.a;
}
ENDCG
}

Fallback "Legacy Shaders/Transparent/Cutout/VertexLit"
}
```

## 对顶点色支持后的shader
```c
// Unity built-in shader source. Copyright (c) 2016 Unity Technologies. MIT license (see license.txt)

Shader "My/Vert Color/Legacy Shaders/Transparent/Cutout/Diffuse" {
Properties {
    _Color ("Main Color", Color) = (1,1,1,1)
    _MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
    _Cutoff ("Alpha cutoff", Range(0,1)) = 0.5
}

SubShader {
    Tags {"Queue"="AlphaTest" "IgnoreProjector"="True" "RenderType"="TransparentCutout"}
    LOD 200

CGPROGRAM
#pragma surface surf Lambert alphatest:_Cutoff vertex:vert

sampler2D _MainTex;
fixed4 _Color;

struct Input {
    float2 uv_MainTex;
	fixed4 my_vertColor;
};

void vert (inout appdata_full v, out Input o) {
    UNITY_INITIALIZE_OUTPUT(Input,o);
    o.my_vertColor=v.color;
}
		 
void surf (Input IN, inout SurfaceOutput o) {
    fixed4 c = tex2D(_MainTex, IN.uv_MainTex) * _Color*IN.my_vertColor;
    o.Albedo = c.rgb;
    o.Alpha = c.a;
}
ENDCG
}

Fallback "Legacy Shaders/Transparent/Cutout/VertexLit"
}
```

## 说明
通过上述两个shader对比，主要加了以下内容。
1，#pragma增加了``vertex:vert``，告诉Unity我们要使用自己的顶点函数vert
2，Input结构增加了``fixed4 my_vertColor``变量，方便后续surf函数进行访问。变量类型为fixed4，则支持rgba
3，实现vert函数，把模型的顶点色存到上述变量my_vertColor中
需要注意的是vert函数内增加了``UNITY_INITIALIZE_OUTPUT(Input,o);``该内置宏用于把变量初始化为0。不增加该宏的话，在编辑器环境下 HLSL编译器编译时 会报错：Shader error in 'My/Vert Color/Legacy Shaders/Transparent/Cutout/Diffuse': 'vert': output parameter 'o' not completely initialized at line 24 (on d3d11)
在HLSLSupport.cginc文件中对该宏有定义：
```c
// Initialize arbitrary structure with zero values.
// Not supported on some backends (e.g. Cg-based particularly with nested structs).
#if defined(UNITY_COMPILER_HLSL) || defined(SHADER_API_PSSL) || defined(UNITY_COMPILER_HLSLCC)
#define UNITY_INITIALIZE_OUTPUT(type,name) name = (type)0;
#else
#define UNITY_INITIALIZE_OUTPUT(type,name)
#endif
```
主要就是因为编辑器选择了使用DX11渲染，需要使用该宏对数据进行初始化

4，最后在surf函数中颜色输出时关联上顶点色即可