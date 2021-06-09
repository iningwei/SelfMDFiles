## TRANSFORM_TEX
TRANSFORM_TEX主要作用是拿顶点的uv去和材质球的tiling和offset作运算， 确保材质球里的缩放和偏移设置是正确的

## _MainTex_ST
_MainTex_ST的ST是应该是SamplerTexture的意思 ，就是声明_MainTex是一张采样图，也就是会进行UV运算。  如果没有这句话，是不能进行TRANSFORM_TEX的运算的。_MainTex_ST.xy为材质球参数中的tiling,_MainTex_ST.zw为材质球参数中的offset。

下面两个函数是等价的：
``o.uv =   TRANSFORM_TEX(v.texcoord,_MainTex); ``
``o.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;``