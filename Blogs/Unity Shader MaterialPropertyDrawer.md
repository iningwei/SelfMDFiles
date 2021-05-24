## 前言
MaterialPropertyDrawer为Shader内置了一些常用的面板，用来实现类似bool,enum这种简单的变量

## 说明
MaterialPropertyDrawer主要提供了4种常用面板
|  类别   | 描述  |
|  ----  | ----  |
| Toggle  | 把类型为float的属性显示为一个开关，值为0或者1。当选中它时，Unity还会设置一个名为**大写属性名_ON**（也可以自定义名字）的shader feature(需要在shader中使用``#pragma shader_feature``或者``#pragma multi_compile``来定义它)。在shader里可以使用#if、#ifdef、#if defined关键字来判断它当前是否开启 |
| Enum  | 把类型为float的属性显示为一个下拉列表，可以使用UnityEngine.Rendering命名空间下的各种状态来设置对应的渲染状态，如ZWrite、ZTest、Blend等。经测试该值目前只支持UnityEngine.Rendering命名空间下的枚举，不支持随便定义 |
| KeywordEnum  | 和Enum类似，不同的是它会定义一组shader keyworld(需要在shader里使用``#pragma multi_compile``或``#pragma shader_feature``来定义它们)。这些keyword的名字是**大写属性名_枚举名**，另外这里在shader中还支持使用#elif来判断使用的是哪个keyword。需要注意的是这里最多同时支持9个keywords |
| PowerSlider  | 显示的是一个非线性响应的滑动条，其参数指定了底数、Range()指定范围。即滑动条上的数值不再是均匀变化的了，而是x<sup>slider</sup>进行变化 |
| IntRange  | 使得滑动条数值变成整数，范围由Range()指定 |
| Space  | 使得Shader属性显示产生一定的间隔，可以设定具体值 |
| Header  | 在属性前增加一个描述文字 |

### Toggle
```csharp
// Will set "_INVERT_ON" shader keyword when set
[Toggle] _Invert ("Invert?", Float) = 0
勾选时，表示使shader关键字 _INVERT_ON生效，关键字格式为：大写属性名_ON
取消勾选时，表示使对应的关键字失效
需要注意的是_INVERT_ON关键字需要使用``#pragma``来定义

// Will set "ENABLE_FANCY" shader keyword when set.
[Toggle(ENABLE_FANCY)] _Fancy ("Fancy?", Float) = 0
```

### Enum
```csharp
// Blend mode values
[Enum(UnityEngine.Rendering.BlendMode)] _Blend ("Blend mode", Float) = 1

// A subset of blend mode values, just "One" (value 1) and "SrcAlpha" (value 5).
[Enum(One,1,SrcAlpha,5)] _Blend2 ("Blend mode subset", Float) = 1
```

### KeywordEnum
```csharp
// Display a popup with None, Add, Multiply choices.
// Each option will set _OVERLAY_NONE, _OVERLAY_ADD, _OVERLAY_MULTIPLY shader keywords.
[KeywordEnum(None, Add, Multiply)] _Overlay ("Overlay mode", Float) = 0

// ...later on in CGPROGRAM code:
#pragma multi_compile _OVERLAY_NONE _OVERLAY_ADD _OVERLAY_MULTIPLY
// ...
```

### PowerSlider
```csharp
// A slider with 3.0 response curve
[PowerSlider(3.0)] _Shininess ("Shininess", Range (0.01, 1)) = 0.08
```

### IntRange
```csharp
// An integer slider for specified range (0 to 255)
[IntRange] _Alpha ("Alpha", Range (0, 255)) = 100
```

### Space
```csharp
// Default small amount of space.
[Space] _Prop1 ("Prop1", Float) = 0

// Large amount of space.
[Space(50)] _Prop2 ("Prop2", Float) = 0
```

### Header
```csharp
[Header(A group of things)] _Prop1 ("Prop1", Float) = 0
```

## 示例
下面示例演示了上述提到的 材质属性Drawer
```csharp
Shader "Custom/Material Property Drawer Example"
{
    Properties
    {
        // Header creates a header text before the shader property.
        [Header(Material Property Drawer Example)]
        // Space creates vertical space before the shader property.
        [Space]

        _MainTex ("Main Tex", 2D) = "white" {}
        _SecondTex ("Second Tex", 2D) = "white" {}

        // Large amount of space
        [Space(50)]

        // Toggle displays a **float** as a toggle. 
        // The property value will be 0 or 1, depending on the toggle state. 
        // When it is on, a shader keyword with the uppercase property name +"_ON" will be set, 
        // or an explicitly specified shader keyword.
        [Toggle] _Invert ("Invert color?", Float) = 0

        // Will set "ENABLE_FANCY" shader keyword when set
        [Toggle(ENABLE_FANCY)] _Fancy ("Fancy?", Float) = 0

        // Enum displays a popup menu for a **float** property. 
        // You can supply either an enum type name 
        // (preferably fully qualified with namespaces, in case there are multiple types), 
        // or explicit name/value pairs to display. 
        // Up to **7** name/value pairs can be specified
        [Enum(UnityEngine.Rendering.BlendMode)] _SrcBlend ("Src Blend Mode", Float) = 1
        [Enum(UnityEngine.Rendering.BlendMode)] _DstBlend ("Dst Blend Mode", Float) = 1
        [Enum(Off, 0, On, 1)] _ZWrite ("ZWrite", Float) = 0
        [Enum(UnityEngine.Rendering.CompareFunction)] _ZTest ("ZTest", Float) = 0
        [Enum(UnityEngine.Rendering.CullMode)] _Cull ("Cull Mode", Float) = 1

        // KeywordEnum displays a popup menu for a **float** property, and enables corresponding shader keyword. 
        // This is used with "#pragma multi_compile" in shaders, to enable or disable parts of shader code. 
        // Each name will enable "property name" + underscore + "enum name", uppercased, shader keyword. 
        // Up to **9** names can be provided.
        [KeywordEnum(None, Add, Multiply)] _Overlay ("Overlay mode", Float) = 0

        // PowerSlider displays a slider with a non-linear response for a Range shader property.
        // A slider with 3.0 response curve
        [PowerSlider(3.0)] _Shininess ("Shininess", Range (0.01, 1)) = 0.08
    }
    SubShader
    {
        Tags { "Queue"="Transparent" "RenderType"="Transparent" }
        Blend [_SrcBlend] [_DstBlend]
        ZWrite [_ZWrite]
        ZTest [_ZTest]
        Cull [_Cull]

        Pass
        {
            CGPROGRAM
            // Need to define _INVERT_ON shader keyword
            #pragma shader_feature _INVERT_ON
            // Need to define _INVERT_ON shader keyword
            #pragma shader_feature ENABLE_FANCY
            // No comma between features
            #pragma multi_compile _OVERLAY_NONE _OVERLAY_ADD _OVERLAY_MULTIPLY

            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            sampler2D _MainTex;
            float4 _MainTex_ST;
            sampler2D _SecondTex;
            float4 _SecondTex_ST;
            float _Shininess;

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float4 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
                o.uv.xy = TRANSFORM_TEX(v.uv, _MainTex);
                o.uv.zw = TRANSFORM_TEX(v.uv, _SecondTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed4 col = tex2D(_MainTex, i.uv.xy);

                // Use #if, #ifdef or #if defined
                #if _INVERT_ON
                col = 1 - col;
                #endif

                // Use #if, #ifdef or #if defined
                #if ENABLE_FANCY
                col.r = 0.5;
                #endif

                fixed4 secCol = tex2D(_SecondTex, i.uv.zw);

                #if _OVERLAY_ADD
                col += secCol;
                #elif _OVERLAY_MULTIPLY
                col *= secCol;
                #endif

                col *= _Shininess;

                return col;
            }
            ENDCG
        }
    }
}
```

## 参考
[Unity官方文档:MaterialPropertyDrawer](https://docs.unity3d.com/ScriptReference/MaterialPropertyDrawer.html)