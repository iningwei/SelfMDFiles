@TOC

## 窗口扩展基础：MenuItem,ScriptableWizard,EditorWindow

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

更多参考：[Editor Windows](https://docs.unity3d.com/2021.3/Documentation/Manual/editor-EditorWindows.html)



## Property Drawers
//TODO：
--TODO:https://docs.unity3d.com/2021.3/Documentation/Manual/editor-PropertyDrawers.html

## Custom Editors
[Custom Editors](https://docs.unity3d.com/2021.3/Documentation/Manual/editor-CustomEditors.html)是相对来说更强大的一种编辑器扩展。主要实现对Inspector界面和SceneView界面的扩展。

下面以一个最简单的实例来说明如何对Inpector界面扩展。
我们定义一个Player:
```csharp
public class Player : MonoBehaviour
{    
    public int armor = 75;
    public int damage = 20;
    public GameObject gun;
} 
```
默认情况下，其对应的Inspector界面如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705114136.png)

要自定义Player组件的Inspector界面，需要新建脚本PlayerEdiotr.cs并继承自Editor。把PlayerEditor.cs放到Editor目录下，代码如下：
```csharp
[CanEditMultipleObjects]
[CustomEditor(typeof(Player))]
public class PlayerEditor : Editor
{
    SerializedProperty armorProp;
    SerializedProperty damageProp;
    SerializedProperty gunProp;

    void OnEnable()
    {
        armorProp = serializedObject.FindProperty("armor");
        damageProp = serializedObject.FindProperty("damage");
        gunProp = serializedObject.FindProperty("gun");
    }

    public override void OnInspectorGUI()
    {
        //更新serializedProperty，一般在OnInspector函数的一开始使用。
        serializedObject.Update();

        //依次重写Player.cs脚本中的armor,damage,gun
        EditorGUILayout.IntSlider(armorProp, 0, 100, new GUIContent("Armor"));
        if (!armorProp.hasMultipleDifferentValues)//当该值相同的时候才显示ProgressBar
        {
            ProgressBar(armorProp.intValue / 100.0f, "ArmorCount");
        }

        EditorGUILayout.IntSlider(damageProp, 0, 100, new GUIContent("Damage"));
        if (!damageProp.hasMultipleDifferentValues)
        {
            ProgressBar(damageProp.intValue / 100.0f, "DamagePower");
        }

        EditorGUILayout.PropertyField(gunProp, new GUIContent("Gun Object"));

        //使更改生效
        serializedObject.ApplyModifiedProperties();
    }

    private void ProgressBar(float value, string str)
    {
        Rect rect = GUILayoutUtility.GetRect(18, 18, "TextField");
        EditorGUI.ProgressBar(rect, value, str);
        EditorGUILayout.Space();
    }
}
```
重写后界面如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705115530.png)


上述代码使用了较常用的SerializedProperty和SerializedObject来实现对Inspector界面的重写。
- [CustomEditor(typeof(Player))] 这个编辑器属性可以把Editor脚本和原始cs脚本建立关系，从而实现重写。

- [CanEditMultipleObjects] 使用了这个编辑器属性，可以确保当选择的多个物体具有相同组件的时候不会报错“Multi-object editing not supported”，而且可以支持多个物体编辑。

- OnInspectorGUI() 重写该方法可以实现对默认Inspector界面的重写。该方法也是最常用的一个方法。

### 还有一种重写Inspector界面的方式
代码如下：
```csharp
[CanEditMultipleObjects]
[CustomEditor(typeof(Player))]
public class PlayerEditor : Editor
{
    Player player;
    //or: Player player { get { return target as Player; } }
    void OnEnable()
    {
        player = target as Player;
    }

    public override void OnInspectorGUI()
    {
        player.armor = EditorGUILayout.IntSlider("Armor", player.armor, 0, 100);
        ProgressBar(player.armor / 100f, "ArmorCount");

        player.damage = EditorGUILayout.IntSlider("Damage", player.damage, 0, 100);
        ProgressBar(player.damage / 100f, "DamagePower");

        bool allowSceneObjects = !EditorUtility.IsPersistent(player);
        player.gun = EditorGUILayout.ObjectField("Gun Object", player.gun, typeof(GameObject), allowSceneObjects) as GameObject;

        //数据有改变的话SetDirty
        if (GUI.changed)
        {
            EditorUtility.SetDirty(target);
        }
    }

    private void ProgressBar(float value, string str)
    {
        Rect rect = GUILayoutUtility.GetRect(18, 18, "TextField");
        EditorGUI.ProgressBar(rect, value, str);
        EditorGUILayout.Space();
    }
}
```
该段代码对应的界面如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705115640.png)

- EditorUtility.IsPersistent(player) 该方法获得目标物体是否存在disk上。官方的解释为：Typically assets like prefabs, textures, audio clips, animation clips, materials are stored on disk.Returns false if the object lives in the scene. Typically this is a game object or component but it could also be a material that was created from code and not stored in an asset but instead stored in the scene.因此EdiotrUtility.IsPersistent(player)返回结果为false。

### 补充一些在OnInspectorGUI()方法中常用到的方法
- DrawDefaultInspector() 绘制默认的对应脚本中的Inspector，常用于扩展一个已经存在的组件：比如想在Camera组件上添加个按钮，实现特殊功能，则可以写个类用[CustomEditor(typeof(Camera))]和Camera建立关系，然后在OnInspector()中先调用DrawDefaultInspector(),然后再添加需要的按钮之类的。

## Scene界面扩展
扩展编辑器的SceneView界面，包括事件（鼠标、键盘）扩展
### 右键菜单
```csharp
public class SceneInterative
{
    [InitializeOnLoadMethod]
    static void Init()
    {
        //Unity2018为SceneView.onSceneGUIDelegate+=
        SceneView.duringSceneGui += SceneView_duringSceneGui;
    }

    private static void SceneView_duringSceneGui(SceneView sceneView)
    {
        //获得当前事件
        Event e = Event.current;
        //鼠标右键放开时，弹出菜单
        if (e != null && e.button == 1 && e.type == EventType.MouseUp)
        {
            //设置右键菜单内容，点击事件，数据
            GenericMenu menu = new GenericMenu();
            menu.AddItem(new GUIContent("菜单项1"), false, OnMenuClick, "menu_1");
            menu.AddItem(new GUIContent("菜单项2"), false, OnMenuClick, "menu_2");
            menu.AddItem(new GUIContent("菜单项3"), false, OnMenuClick, "menu_3");
            menu.ShowAsContext();

            //必须调用Use()方法，完成这次事件的使用，否则会误伤到Unity编辑器Scene视图内自带的右键事件
            e.Use();
        }
    }


    static void OnMenuClick(object userData)
    {
        EditorUtility.DisplayDialog("Tip", "OnMenuClick" + userData.ToString(), "Ok");
    }
}
```
如上代码实现了在Scene界面内点击鼠标右键后弹出右键菜单，同时又不影响编辑器自带的鼠标交互。
- 在duringSceneGui回调中获得当前的交互事件``Event e=Event.current;``,通过判断e.isKey, e.button, e.character，e.type来获得对应的鼠键操作
- e.button的值:0为鼠标左键，1为鼠标右键，2为鼠标中键
- 有时候我们会在Inspector界面内点击某个按钮后触发Scene界面交互，这个时候需要强制获得Scene的焦点，可以使用：``(SceneView.sceneViews[0] as SceneView).Focus();``
- SceneView.RepaintAll();可以用来刷新Scene窗口
- Scene界面内获得当前鼠标所在位置发射的射线：``Ray ray=HandleUtility.GUIPointToWorldRay(e.mousePosition);``
- Scene内世界坐标转换为屏幕GUI坐标:``HandleUtility.WorldToGUIPoint(worldPos);``

### OnSceneGUI
OnSceneGUI()可以丰富Scene界面交互，一般用于继承自Editor类重写Inspector界面的类中，选中目标物体，才可触发OnSceneGUI()方法。（该方法实际上是和上面提到的Custom Editor配套的）。
[Handles](https://docs.unity3d.com/2021.3/Documentation/ScriptReference/Handles.html)类中定义了很多可以直接在OnSceneGUI()方法中使用的方法。

这里以上述Player: MonoBehaviour类为例，进行扩展。
```csharp
    Rect screenOrigo;
    void OnSceneGUI()
    { 
        screenOrigo = Camera.current.pixelRect;

        Handles.BeginGUI();//2D GUI物体(GUI or EditorGUI)的绘制必须放在BeginGUI()和EndGUI()之间。        
        var guiPoint = HandleUtility.WorldToGUIPoint(player.transform.position);
        GUI.color = Color.green;
        GUI.Label(new Rect(guiPoint.x-20,guiPoint.y-50,100,30), player.transform.name);
        if (GUI.Button(new Rect(guiPoint.x-30,guiPoint.y,30,20),"x+"))
        {
            player.transform.position = new Vector3(player.transform.position.x + 1, player.transform.position.y, player.transform.position.z);
        }
        if (GUI.Button(new Rect(guiPoint.x + 10, guiPoint.y, 30, 20), "x-"))
        {
            player.transform.position = new Vector3(player.transform.position.x - 1, player.transform.position.y, player.transform.position.z);
        }
        GUI.color = Color.white;
        if(GUI.Button(new Rect(screenOrigo.width-100,screenOrigo.height-50, 80, 30), "Reset"))
        {
            player.transform.position = Vector3.zero;
        }
        Handles.EndGUI();
    }
```
当把Player类附加到某Cube上后并选中该Cube，SceneView界面如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705180417.png)

实质上OnSceneGUI()中绘制的内容也属于Gizmos，也会受编辑器中开关Gizmos的显示控制。

### 获得SceneView界面的尺寸
Screen.width获得尺寸是对的，但是Screen.height获得的尺寸是包含了界面顶部标签区域和快捷键区域（即ribbon区）。
而OnSceneGUI()界面内添加GUI时(0，0)点不是从界面最左上点开始的，而是从空白区域算起的。因此获得空白区域的尺寸至关重要。
可以通过``Camera.current.pixelRect``获取，参考[how-to-find-out-the-real-viewportscreen-size-in-sc](https://answers.unity.com/questions/63361/how-to-find-out-the-real-viewportscreen-size-in-sc.html)


## TreeView
[TreeView](https://docs.unity3d.com/2021.3/Documentation/Manual/TreeViewAPI.html)

## Gizmos&Handles
二者都可以扩展SceneView界面的显示。
### Gizmos
可以在SceneView绘制line、sphere、icon、texture、mesh等用来丰富界面显示，方便调试等。
绘制需要在``OnDrawGizmos``或者``OnDrawGizmosSelected``方法中进行。
- OnDrawGizmos() 每帧都会调用
- OnDrawGizmosSelected() 只有当附着了组件的物体被选中后才会调用

如下代码，在GameObject周围绘制一个10单位的cube（附着该组件的物体选中后才会显示Gizmos）
```csharp
public class GizmosExample : MonoBehaviour
{
    void OnDrawGizmosSelected()
    {
        // Draw a yellow cube at the transform position
        Gizmos.color = Color.yellow;
        Gizmos.DrawWireCube(transform.position, new Vector3(10, 10, 10));
    }
}
```

#### 另一种Gizmos显示方式
如下建立一个TestDrawGizmo.cs的脚本，放到Editor目录下。
```csharp
public class TestDrawGizmo
{
    [DrawGizmo(GizmoType.NotInSelectionHierarchy | GizmoType.Pickable | GizmoType.InSelectionHierarchy)]
    static void RenderLightGizmo(Light light, GizmoType gizmoType)
    {
        Handles.Label(light.transform.position, light.transform.gameObject.name);
        Gizmos.DrawIcon(light.transform.position + Vector3.up, "Light.png");
        if ((gizmoType & GizmoType.InSelectionHierarchy) != 0)
        {
            if ((gizmoType & GizmoType.Active) != 0)
            {
                Gizmos.color = Color.red * 0.4f;
            }
            else
                Gizmos.color = Color.red * 0.5f;

            Gizmos.DrawSphere(light.transform.position, light.range);
        }
    }
}
```
当我们选中Light组件对应的物体时，SceneView界面内结果如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705185822.png)

### Handles
在OnSceneGUI()方法中调用，用来丰富SceneView界面的显示。[官方示例](https://docs.unity3d.com/2021.3/Documentation/Manual/GizmosAndHandles.html)

## 一些实用Attribute
- [InitializeOnLoadMethod]
在Editor Class内部修饰静态方法，起到自动调用目标方法的功能。
- [InitializeOnLoad]
修饰Editor Class，和类的静态构造函数结合使用，可以达到和[InitializeOnLoadMethod]一样的目的。

## EditorGUILayout和GUILayout
- EditorGUILayout.Separator() 一个比较大的空行

- EditorGUILayout.Space() 一个比较小的空行

- GUILayout.Space(10f) 可控具体空多少行

- GUILayout.BeginHorizontal()和GUILayout.EndHorizontal() 二者一起可以让内部GUI水平排列。同理还有Vertical。



## 继承关系
``ScriptableWizard:EditorWindow:ScriptableObject:Object``
``Editor:ScriptableObject:Object``