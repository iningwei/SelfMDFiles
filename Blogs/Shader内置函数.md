[微软DX HLSL内置函数文档](https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl-intrinsic-functions)

 step(a,b) b >= a ? 1:0

smoothstep(x,y,a); //a如果小于x返回0 如果a>y返回1 在x y之间返回 3a^2-2a^3

saturate(x);//如果x取值小于0，则返回值为0。如果x取值大于1，则返回值为1。若x在0到1之间，则直接返回x的值.）

lerp(a,b,w); //(a,b,w)当w为0时返回a,为1时返回b,当w在0-1之间时，比重w将ab进行线性插值，可以实现由快变慢的效果

fract(x);//返回x的小数部分

sign(x);//x少于0返回-1.0 ,x等于0则返回0.0 ,x大于0则返回1.0

clamp(x,a,b)：x如果小于a返回a，如果大于b返回b，在a~b范围内返回x

fwidth(x);//就是取相邻两个像素（物理设备）该变量的差值，ddx就是x轴的差值，ddy就是y轴的差值，对应的也就是偏导数。Returns abs(ddx(x)) + abs(ddy(x))

pow(x, y): x的y次方；

smoothstep与lerp类似，返回 0 和 1 之间平滑的 Hermite 插值