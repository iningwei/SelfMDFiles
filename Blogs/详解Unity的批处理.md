动态批处理要求300 vertices

## 合批失败原因汇总
由于官方使用的是5.6.0b6进行的总结，因此笔者使用Unity5.6.7f1和Unity2020.3.2f1两个版本进行了对比测试。
- Additional Vertex Streams
Demo中是自带的两个Cube，按理说会自动的动态合批。但是在代码层使用了[MeshRenderer.additionalVertexStreams](https://docs.unity3d.com/2017.3/Documentation/ScriptReference/MeshRenderer-additionalVertexStreams.html)设定了额外的顶点信息流。
笔者在两个版本中Frame Debug给的合批失败的的提示内容都是:The material doesn't have GPU instancing enabled。设置Enable Instancing后，Instancing成功。
总结：顶点信息流使动态批处理失败，但是并不影响Instancing合批。

- Deferred Objects on Different Lighting Layers
Demo中是两个相同的Cube，有不同的Layer设置，其中一个Cube的Layer接受了光照。造成动态合批失败。
设置Enable Instancing依旧无法合批。
两个Unity版本两种不同的操作的提示内容都是：Objects are on different lighting layers.

- Deferred Objects Split by Shadow Distance
Demo中是两个相同的Cube其中一个在光照的阴影范围内，即在相机下是可以产生阴影的。另一个距离相机较远，不产生阴影。
在Unity5.6.7f1中动态批处理合批失败，提示为：Some objects are within the shadow distance,while some other objects are not.当启动Instancing，依旧无法合批，报相同的提示信息。
在Unity2020.3.2f1中却可以动态批处理成功；而且当启动Instancing后，会执行Instancing合批，即Instancing合批优先级更高。

- Different Combined Meshes
Demo中有两个父物体，父物体下都有两个子物体：一个Cube和一个Sphere。父物体上调用了``StaticBatchingUtility.Combine(gameObject);``进行代码层的静态合批。两个被合批的物体无法进行批处理。
在Unity5.6.7f1中的提示信息为：The combined mesh is different between static-batched objects.
在Unity2020.3.2f1中的提示信息为：Objects belong to different static batches.


- Different Custom Properties
Demo中两个相同的Cube，代码层使用MaterialPropertyBlock为两个Cube设置了不同的颜色；导致动态批处理失败。两个版本的Unity提示信息均为：Objects have different Material PropertyBlock set.
这种情况下要合批可以考虑开启Instancing，同时要扩展Shader使支持颜色属性。

- Different Lights 
Demo中两个相同的Cube，被不同的光源影响。导致动态批处理失败。两个版本Unity的提示信息一致：Objects are affected by different forward lights。
经扩展测试，GPU Instancing这种情况下也会失败。

- Different Materials
Demo中两个Cube，虽然Mesh网格一致，但是使用了不同的材质球，导致动态批处理失败。提示信息均为：Objects have different matierials

- Different Reflection Probes 
Demo中两个相同Cube，两个反射探针分别罩着它们。动态批处理失败。
两个版本Unity提示信息均为：Objects are affected by different reflection probes.
经扩展测试，GPU Instancing这种情况下也会失败。


- Different Shadow Caster Hash
Demo场景中两个Cube，使用了不同的材质球，均使用了Stanard Shader的Cutout模式，但是Alobedo的颜色和Alpha Cutoff的值设置不同。另外场景中有Directional灯光，且对两个Cube均有影响。
由于使用的材质球不一样，因此在绘制两个Cube的时候无法动态合批。提示信息都是：Objects have different matierials
由于灯光的影响，会产生阴影，两个Material的shader参数设置不一致会影响阴影投射pass导致阴影这一块无法合批，两个版本Unity的提示信息都是：Objects either have different shadow caster shaders,or have different shader properties/keywords that affect the output of the shadow caster pass.
扩展测试把两个材质球的参数调整一致后，两个Cube的阴影实现了合批。

- Different Shadow Receiving Settings
Demo场景中两个Cube，唯一的区别是一个设置了接受阴影，一个设置了不接受阴影。导致动态合批失败。提示信息均为：Either objects have different "Receive Shadows" settings,or some objects are within the shadow distance,while some other objects are not.
扩展测试，GPU Instancing也不生效。

- Different Static Batching Flags 
Demo场景中三个相同Cube，其中两个设置了``Batching Static``,运行后，设置了静态批处理的两个Cube合批，未设置的无法和它们合批，且有提示信息：Objects have different batching-static settings.
扩展测试，若Enable GPU Instancing开启，那么两个设置了静态批处理的依旧会合批，未设置的无法合批，但是没有了提示信息。


- Dynamic Batching Disabled to Avoid Z-Fighting 
Demo场景中两个相同Cube，shader使用的是Unlit/Color,相机的渲染路径选择了Deferred。两个版本中的合批提示都是：
Dynamic Batching is turned off in the Player Settings or is disabled temporarily in the current context to avoid z-fighting.
若渲染路径改成Forward,shader使用Unlit/Color，那么动态合批成功。
若渲染路径使用Deferred,shader使用standard，那么动态合批成功。
至于为什么Deferred渲染路径和Unlit/Color会导致Z-Fighting,目前笔者不得而知，希望知道的大佬不吝赐教。

- Instancing Different Geometries
Demo场景中两个Cube,两个Sphere，使用同一个材质球，开启了GPU instancing。两个Cube之间合批成功，两个Sphere之间合批成功。它们彼此无法合批，因为Mesh不一致。提示信息为：Rendering different meshes or submeshes with GPU instancing.


- Lightmapped Objects
场景中有三个Cube, 其中两个缩放一致，另一个比较高,其它设置一样。场景中的灯光使用的是Baked模式。
在Unity5.6.7f1中三个Cube都勾选了Lightmap Static。
在Unity2020.3.2f1中三个Cube勾选的是Contribute GI 和Reflection Probe Static。（这里不确定低版本往高版本转换后的设置对否）
由于光照贴图不兼容，因此在新版本Unity中，Lighting->Baked Lightmaps面板中重新生成了光照贴图。
三个Cube无法动态合批，提示信息为：Objects are lightmapped。
扩展测试，开启GPU Instancing也无法合批


- Lightprobe Affected Objects
这个案例没看懂#！

- Mixed Sided Mode Shadow Casters 
Demo场景中两个Quad，其中一个Cast Shadows设置为On，另一个设置为Two Sided。两个模型的渲染动态合批。但是阴影的渲染无法合批，提示信息为：Objects have different "Cast Shadows" settings.


- Multipass
Demo场景中两个相同Cube，使用自定义Shader，shader由3个pass组成。无法动态合批，提示信息：An object is using a multi-pass shader.

- Multiple Forward Lights
Demo场景中有一个Cube使用的是Standard shader，三个灯光，其中一个平行光，两个Spot光；相机Rendering Path为Forward。因此三个灯光都会运算一次，使得一个Cube被绘制3次。提示信息为：An object is affected by multiple forward lights.
扩展测试，开启GPU Instancing依旧会被绘制3次。


- Non-instanceable Property Set 


- Odd Negative Scaling 
- Shader Disables Batching
- Too Many Indices in Dynamic Batch
- Too Many Indices in Static Batch
- Too Many Vertex Attributes for Dynamic Batching
- Too Many Vertices for Dynamic Batching

## 补充
- Objects are rendered using different rendering functions