ScriptableObject是Unity提供的数据资源化的一种灵活方式，它的使用非常简单
## 定义一个ScriptableObject资产
类要成为资产，那么需要让其继承自ScriptableObject。
```csharp
[System.Serializable]
public class MyData
{
    public string name;
    public int age;
}

[CreateAssetMenu(fileName = "MyScriptableObject", menuName = "MySubMenu/Create MyScriptableObject")]
public class MyScriptableObject : ScriptableObject, ISerializationCallbackReceiver
{
    public float someVariable;
    public MyData myData;


    private void OnEnable()
    {

    }
    private void OnValidate()
    {

    }

    private void OnDisable()
    {
        
    }

    private void OnDestroy()
    {

    }


    public void OnAfterDeserialize()
    {
        Debug.Log("MyScriptableObject after deserialize");
    }

    public void OnBeforeSerialize()
    {

    }
}
```

- 标签CreateAssetMenu用来提供Editor环境下Project面板内通过右键创建asset的方式
- ScriptObject的回调较少，只有OnEnable、OnDisable、OnDestroy、OnValidate四个，而且其中的OnValidate只能在编辑器环境下使用
- 上述代码演示了通过实现ISerializationCallbackReceiver接口，获得序列化的回调
- 上述示例SriptableObject中引入了类MyData，需要设置MyData为Serializable，否则MyData无法序列化，且在编辑面板也不可见

## 创建方式
- CreateInstance加载
通过脚本``var so=ScriptableObject.CreateInstance<>();``获得实例。
需要保存的话使用``AssetDatabase.CreateAsset(so, path);``来保存，该方法只能在编辑器环境使用，path形如：``Assets/xxx/xxx.asset``。


- 资产加载
由于其和普通的音效、图片等资产类似。因此根据ScriptObject实体资产的位置和状态，可以考虑使用Resources.Load、AB、或者io的方式加载。

## 生命周期
- 当它是被绑定到.asset文件或者AssetBundle等资源文件中的时候，它就是persistent的，这意味着 

    - 它可以通过Resources.UnloadUnusedAssets来被unload出内存

    - 可以通过脚本引用或其他需要的时候被再次load到内存
- 如果是通过CreateInstance<>来创建的，它就是非persistent的，这意味着 

   - 它可以通过GC被直接destroy掉（如果没有任何引用的话）

   - 如果不想被GC的话，可以使用[HideFlags.HideAndDontSave](https://docs.unity3d.com/ScriptReference/HideFlags.html)

## Inspector面板重写
和继承自MonoBehavior的类一样，其也支持Inspector面板重写

## 保存
上文提到可以使用AssetDatabase.CreateAsset()方法进行文件保存。但是对于硬盘中已存在的ScriptableObject，当我们选中其后并在Inspector面板内修改后，编辑器是可以自动识别"脏数据"并使用*符号提示保存的。
当然用户也可以通过代码方式保存：
```csharp
EditorUtility.SetDirty(so);   
AssetDatabase.SaveAssets();
```

## ScriptObject不可运行时自动保存数据
运行时对数据的修改无法保存，如果有需要，目前主流的方法是使用[JsonUtility](https://docs.unity3d.com/ScriptReference/JsonUtility.html?_ga=2.12333684.135331608.1614146309-299171731.1595242112)把ScriptObject存为Json字符串，使用``FromJsonOverwrite``方法再转成ScriptObject。

参考文档:[savingloading-scriptableobject-during-runtime](https://answers.unity.com/questions/896298/savingloading-scriptableobject-during-runtime.html)
