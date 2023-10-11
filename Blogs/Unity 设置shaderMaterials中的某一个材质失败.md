``renderer.sharedMaterials[item.matIndex] = renderMat;``第一次设置成功，后续设置皆不生效。

通过打印，可以发现设置后``renderer.sharedMaterials[item.matIndex]``为null。

解决办法，先缓存材质数组，对材质数组进行修改，再把材质数组整体设置回去：
```csharp
Material[] sharedMaterialsCopy = renderer.sharedMaterials;
sharedMaterialsCopy[item.matIndex] = renderMat;
renderer.sharedMaterials = sharedMaterialsCopy;
```  

缘由：
r.sharedMaterials[someNumber] = someMaterial doesn't work because sharedMaterials doesn't return an reference to the renderers shared materials, but instead returns a new array containing the references to the shared materials. Thus assigning anything to this array does nothing as it immediatly goes out of scope.

ref:https://forum.unity.com/threads/material-sharedmaterial-sharedmaterials-confusion.4802/