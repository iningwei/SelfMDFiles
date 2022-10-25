已知物体的旋转欧拉角，但是获得不了该物体的GameObject对象，那么如何只根据该欧拉角获得物体的forward呢？
方法如下：
```csharp
Vector3 eulerAngel;
Quaternion quaternion = Quaternion.Euler(eulerAngel.x, eulerAngel.y, eulerAngel.z);
Vector3 forward = quaternion * Vector3.forward;
```