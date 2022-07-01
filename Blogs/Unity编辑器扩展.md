@TOC

## 窗口扩展三大件：MenuItem,ScriptableWizard,EditorWindow

### MenuItem
使用MenuItem标识可以为编辑器添加新的菜单,也可以为Inspector上下文添加菜单。点击后执行一些特定的逻辑，没有额外的操作界面。只有静态方法可以使用该标识，该标识可以把静态方法转换为菜单命令。
另外可以为菜单创建快捷方式(hotkey),你可以使用如下特殊修饰字符：%(Windows系统上的ctrl,OS X系统上的cmd),#(shift),&(alt)。比如快捷方式“shift-alt-g”的写法为[MenuItem(“XX/XX/XXX #&g”)]。如果不需要为hotkey提供修饰符需要用字符_修饰，如快捷方式“g”的写法为[MenuItem(“XX/XX/XXX _g”)]
另外还可以支持一些特殊的字符：LEFT、RIGHT、UP、DOWN、F1…F12、HOME、END、PGUP、PGDN。
示例代码:
```csharp
using UnityEngine;  
using System.Collections;  
using UnityEditor;  

public class AddChild
{
    [MenuItem("Custom/Create Child For Selected GameObjects")]
    static void MenuAddChild()
    {
        Transform[] transforms = Selection.GetTransforms(SelectionMode.TopLevel | SelectionMode.OnlyUserModifiable);
        foreach (Transform transform in transforms)
        {
            GameObject aChild = new GameObject("_Child");
            aChild.transform.parent = transform;
        }
    }
    [MenuItem("Custom/Create Child For Selected GameObjects", true)]
    static bool ValidateMenuAddChild()
    {
        return Selection.activeGameObject != null;
    }
}
```
上述示例可以在Unity菜单项创建新的目录菜单：``Custom/Create Child For Selected GameObjects``,同时提供了Validate函数确保当前若没有物体被选中的话，该菜单项是灰化不可点击的。

需要注意的是，入口函数和Validate函数都必须是静态的，Validate函数名由Validate和入口函数名组合而成，Validate函数的MenuItem标识中的第1个参数和入口函数的一致，第2个参数为true。

#### 排序、分组
MenuItem函数原型为：public MenuItem(string itemName, bool isValidateFunction, int priority);易知通过控制第3个参数便可以设置分组。
第3个int参数，值越小越排在上面，Unity会自动分组，50为单位。如下Option1和Option2会自动被分成不同的组，具体表现就是有个分割线。
```csharp
[MenuItem("NewMenu/Option1", false, 3)]
private static void NewMenuOption1()
{
}

[MenuItem("NewMenu/Option2", false, 51)]
private static void NewMenuOption2()
{
}
```


#### 一些特定目录结构
上述提到通过MenuItem标识可以为Unity创建新的目录，不仅如此还可以为已有的菜单增加内容。
- MenuItem("GameObject/") 新建菜单项出现在GameObject节点下。
- MenuItem("Assets/") 新建菜单项出现在Assets节点下（Project面板下右键弹出的面板内容和Assets节点展开的是一致的）
- MenuItem("CONTEXT/组件名/XXX") 可以为某个具体组件的Inspector上下文菜单增加内容。打开方式为右键组件或者左键组件最右边的“齿轮”按钮，需要注意的是Inspector上下文添加菜单不支持多重层次。
如下代码为Transform组件扩展其Inspector上下文菜单：
```csharp
public class AddChild
{
    [MenuItem("CONTEXT/Transform/Create Child For Selected GameObjects")]
    static void MenuAddChild()
    {
        Transform[] transforms = Selection.GetTransforms(SelectionMode.TopLevel | SelectionMode.OnlyUserModifiable);
        foreach (Transform transform in transforms)
        {
            GameObject aChild = new GameObject("_Child");
            aChild.transform.parent = transform;
        }
    }
    [MenuItem("CONTEXT/Transform/Create Child For Selected GameObjects", true)]
    static bool ValidateMenuAddChild()
    {
        return Selection.activeGameObject != null;
    }
}
```
如下代码为获得Inspector上下文菜单对应的组件：
```csharp
[MenuItem("CONTEXT/Rigidbody/DoSomething")]
static void DoSomething(OnInspectorGUI command)
{
    Rigidbody body=command.context;
    body.mass=5;
}
```

### ScriptableWizard
通过继承ScriptableWizard可以创建编辑器向导，Unity已经为我们封装好了一些变量、方法、消息。如：
- 变量：errorString(设置向导的错误提示信息)、helpString(设置向导的帮助提示)、isValid(可以控制向导的Create Button和Other Button能否点击)。
- 静态方法：DisplayWizard
- 消息：OnWizardCreate(当点击Create按钮的时候触发)、OnWizardOtherButton(当点击Other按钮的时候)、OnWizardUpdate(当向导打开的瞬间或者向导中有输入参数的变化时触发)。

示例：
```csharp
public class CreateACube : ScriptableWizard
{
    public float size = 1f;//声明为public可以被向导序列化显示在界面上，且可以改变其值
    [MenuItem("Custom/CreateACube")]
    static void CreateACubeWizard()
    {        
        ScriptableWizard.DisplayWizard("创建一个Cube", typeof(CreateACube), "确定", "取消");
        //OR
        //ScriptableWizard.DisplayWizard<CreateACube>("创建一个Cube", "确定", "取消"); 
    }

    void OnDrawGizmos()
    {
        if (size < 3)
        {
            return;
        }
        else
        {
            Gizmos.color = Color.red;
            Gizmos.DrawCube(Vector3.zero, new Vector3(size, size, size));
        }
    }

    void OnWizardCreate()
    {
        GameObject obj = GameObject.CreatePrimitive(PrimitiveType.Cube);
        obj.transform.localScale = new Vector3(size, size, size);
    }

    void OnWizardOtherButton()
    {
        Close();//点击otherButton,需要调用Close()方法，才关闭向导。但是点击createButton，并无需调用Close()方法而自动关闭。
    }

    void OnWizardUpdate()
    {
        helpString = "输入Cube的Size,Size要大于等于3,创建一个Cube";

        if (size < 3)
        {
            errorString = "size要大于等于3";
            isValid = false;
        }
        else
        {
            errorString = "";
            isValid = true;
        }
    }
}
```
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220701183452.png)

### EditorWindow
继承自EditorWindow的类，可以实现更复杂的编辑器窗口功能。且这种窗口是可以自由内嵌到Unity编辑器内，共同组成编辑器的Layout。
通过在OnGUI()函数内调用GUILayout、EditorGUILayout、GUI等类的一些方法来实现复杂的界面。
下面通过EditorWindow来实现上述的编辑器向导，相关代码如下：
```csharp
public class CreateACube2 : EditorWindow
{
    float size = 1f;
    string helpString = "输入Cube的Size,Size要大于等于3,创建一个Cube";
    string errorString = "size需要大于等于3";
    bool enableConfromButton = false;
    static Color originColor;
    [MenuItem("Custom/CreateACube2")]
    static void Init()
    {
        //获取已经打开的window,如果不存在则new一个
        CreateACube2 window = EditorWindow.GetWindow(typeof(CreateACube2)) as CreateACube2;
        originColor = GUI.color;
    }

    void OnGUI()
    {
        GUILayout.Label(helpString, EditorStyles.boldLabel);
        size = EditorGUILayout.FloatField("size:", size);

        enableConfromButton = size >= 3 ? true : false;
        if (enableConfromButton)
        {
            GUI.enabled = true;
        }
        else
        {
            GUI.enabled = false;//设置error的时候Button按钮不可点击
            GUI.color=Color.red;//设置errorString的颜色为红色
            GUILayout.Label(errorString);
        }
        GUI.color = originColor;
        if (GUILayout.Button("确定"))
        {
            DoCreate();
        }
    }

    void DoCreate()
    {
        GameObject obj = GameObject.CreatePrimitive(PrimitiveType.Cube);
        obj.transform.localScale = new Vector3(size, size, size);
    }
} 
```
除了可以使用OnGUI()回调，还有如下一些函数可以丰富EditorWindow：
- OnDestroy()当EditorWindow关闭的时候触发。
- OnFocus()当Window获得焦点触发。
- OnHierarchyChange()当Hierarchy面板内有改变的时候触发。
- OnInspectorUpdate()每秒十帧的触发，来判断Inspector界面是否有改变。
- OnLostFocus()当Window失去焦点触发。
- Update()每秒100次的触发，在所有可见的Windows。

#### 继承关系
ScriptableWizard:EditorWindow:ScriptableObject:Object



--TODO：http://bbbbbbion.github.io/2015/08/30/Unity3D%E7%BC%96%E8%BE%91%E5%99%A8%E6%89%A9%E5%B1%95/