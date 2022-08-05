https://www.jianshu.com/p/93dbed286af9

https://www.jianshu.com/p/c761f2282c70
https://blog.csdn.net/RandomXM/article/details/88642534
https://blog.csdn.net/YN_TCDJ/article/details/78426012
https://blog.csdn.net/YN_TCDJ/article/details/78426012
https://zhuanlan.zhihu.com/p/108259045

https://www.cnblogs.com/murongxiaopifu/p/9243540.html
https://answers.unity.com/questions/1415235/objects-have-different-materials-on-same-material.html

## step
step函数时阶跃函数，格式为step(a,b)，其函数逻辑为：
```csharp
float step(float a,float b)
{
    return b>a?1:0
}
```
## saturate
saturate是一个对数值进行[0,1]规范的函数，格式为saturate(a),其函数逻辑为：
```csharp
float saturate(float a){
    if(a<0){
        return 0;
    }
    if(a>1){
        return 1;
    }
    return a;
}
saturate函数同样支持变量的swizzled版本，如``saturate(color.rgb)``
```

## smoothstep
smoothstep是一个三次插值的平滑函数，格式为smoothstep(a,b,x)，其函数逻辑为：
```csharp
float smoothstep(float a,float b,float x){
    float t=saturate((x-a)/(b-a));
    return 3.0*t*t-2.0*t*t*t
}
```
另有一说函数逻辑为：
```csharp

```


smoothstep(x,y,a); //a如果小于x返回0 如果a>y返回1 在x y之间返回 3*a^2 - 2*a^3

saturate(x);//如果x取值小于0，则返回值为0。如果x取值大于1，则返回值为1。若x在0到1之间，则直接返回x的值.）

lerp(a,b,w); //(a,b,w)当w为0时返回a,为1时返回b,当w在0-1之间时，比重w将ab进行线性插值，可以实现由快变慢的效果

frac(x);//返回x的小数部分

sign(x);//x少于0返回-1.0 ,x等于0则返回0.0 ,x大于0则返回1.0

clamp(x,a,b)：x如果小于a返回a，如果大于b返回b，在a~b范围内返回x

fwidth(x);//就是取相邻两个像素（物理设备）该变量的差值，ddx就是x轴的差值，ddy就是y轴的差值，对应的也就是偏导数。Returns abs(ddx(x)) + abs(ddy(x))

pow(x, y): x的y次方；

smoothstep与lerp类似，返回 0 和 1 之间平滑的 Hermite 插值
clip(x):x若小于0，则放弃当前像素。一般用于alpha test.
abs(x)


参考文档：
[微软DX HLSL内置函数文档](https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl-intrinsic-functions)