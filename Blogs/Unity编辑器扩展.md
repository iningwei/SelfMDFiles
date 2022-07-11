[TOC]
## 继承自 MonoBehaviour 的类的一些扩展
一方面继承自MonoBehaviour的类都会默认被添加到 Component->Scripts 节点下。若该类使用[AddComponentMenu("XX/XX/XX")]修饰后，则会为其添加到已经存在的节点下。

另一方面MonoBehaviour类都可以添加到GameObject作为Component，在Inspector界面可以显示该类的一些信息。
所有``继承自UnityEngine.Object的类，如GameObject,Component,MonoBehaviour,Texture2D,AnimationClip;所有基本类型，如int,string,float,bool;一些内建类型，如Vector2,Vector3,Quaternion,Color,Rect,Layermask;序列化类型的Array,序列化类型的List;枚举Enum``。默认情况下这些类型的public变量是可以在Inspector界面显示的，同时编辑器对其进行序列化。
### 一些涉及到变量显示和序列化的属性
- [HideInInspector] 变量依旧可以被序列化，但是在Inspector界面上不可见
- [NonSerialized] 变量不可被序列化，且在Inspector面板上不可见
- [SerializeField] 可以把private类型变量变成可序列化的，在Inspector面板可见。（虽然Inspector面板可见，但仍然是private变量）


### ContextMenu属性
继承自MonoBehaviour的类的内部，可以为其函数添加``ContextMenu``属性，在编辑器界面下执行一些操作。
```csharp
public class TestContextMenu : MonoBehaviour
{
    [ContextMenu("DoLogTest")]
    void DoLogTest()
    {
        Debug.Log("i am function DoLogTest");
    }
}
```
如上述代码，当把TestContextMenu脚本附加到GameObject上后，在GameObject的Inspector界面中会有TestContextMenu组件的信息，右键该组件（或者左键点击3个.按钮）会看到DoLogTest菜单项，点击后会按照预期打印日志。

### ContextMenuItem属性
可以为变量(public)添加右键弹出命令，从而执行相关的操作。
该标识接受两个变量，1个是display name，一个是右键弹出菜单点击后的方法。
```csharp
public class Test : MonoBehaviour
{
    [ContextMenuItem("Random Age", "RandomAge")]
    public int Age;
    void RandomAge()
    {
        Age = new System.Random(DateTime.Now.Millisecond).Next(1, 100);
    }

    [ContextMenuItem("Random Name", "RandomName")]
    public string Name;
    private void RandomName()
    {
        string[] names = new string[] { "Jack", "Jim", "Tomas", "Han", "Ann" };
        Name = names[new System.Random(DateTime.Now.Millisecond).Next(0, 4)];
    }
}
```
上述代码分别为Age和Name这两个字段添加了右键弹出菜单的功能。

### Range属性
[Range(min,max)]或者[RangeAttribute(min,max)]可以对变量的输入范围进行限定，使得Inspector检视面板内的数值输入框变成Slider,且范围为(min,max)。

### Header属性
[Header("")] Inspector面板中在目标字段顶部展示额外的说明文字

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

#### 勾选
下述代码实现Test1和Test2两项，互斥选择，且默认情况下选择Test1
```csharp
[InitializeOnLoad]
public class TestMenuItem  
{
    static string menuPath1 = "Custom/Test1";
    static string menuPath2 = "Custom/Test2";
    static TestMenuItem()
    {
        //默认选择Test1
        if (Menu.GetChecked(menuPath1) == false && Menu.GetChecked(menuPath2) == false)
        {
            Menu.SetChecked(menuPath1, true);
        }
    }
    [MenuItem("Custom/Test1", false, 1)]
    static void Test1()
    {
        if (Menu.GetChecked(menuPath1)==false)
        {
            Menu.SetChecked(menuPath1, true);
            Menu.SetChecked(menuPath2, false);
        }
    }
    [MenuItem("Custom/Test2", false, 2)]
    static void Test2()
    {
        if (Menu.GetChecked(menuPath2)==false)
        {
            Menu.SetChecked(menuPath1, false);
            Menu.SetChecked(menuPath2, true);
        }
    }
}
```

#### 一些特定目录结构
上述提到通过MenuItem标识可以为Unity创建新的目录，不仅如此还可以为已有的菜单增加内容。
- MenuItem("GameObject/") 新建菜单项出现在GameObject节点下。
- MenuItem("Assets/") 新建菜单项出现在Assets节点下（Project面板下右键弹出的面板内容和Assets节点展开的是一致的）
- MenuItem("CONTEXT/组件名/XXX") 可以为某个具体组件的Inspector上下文菜单增加内容。打开方式为右键组件或者左键组件最右边的“齿轮”按钮(较新版本Unity为从上到下的3个.按钮)，需要注意的是Inspector上下文添加菜单不支持多重层次。
如下代码对Transform组件扩展其Inspector上下文菜单：
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

#### 代码执行菜单项
EditorApplication.ExecuteMenuItem(“XX/XX/XX”)
 
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
        //base.OnInspectorGUI(); //调用父类方法绘制一次GUI，Player中原本的可序列化数据等会在这里绘制一次。 如果不调用父类方法，则这个Mono的Inspector全权由下面代码绘制。

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
扩展编辑器的SceneView界面
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
            menu.AddItem(new GUIContent("Home"), false, OnMenuClick, "menu_1");
            menu.AddItem(new GUIContent("Skill/Skill1"), false, OnMenuClick, "menu_2");
            menu.AddItem(new GUIContent("Skill/Skill2"), false, OnMenuClick, "menu_3");
            menu.ShowAsContext();

            //必须调用Use()方法，完成这次事件的使用，否则会误伤到Unity编辑器Scene视图内自带的右键事件
            e.Use();
        }
    }


    static void OnMenuClick(object userData)
    {
        EditorUtility.DisplayDialog("Tip", "OnMenuClick:" + userData.ToString(), "Ok");
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

#### 一种自定义菜单的方式
上述使用GenericMenu来创建菜单并添加菜单项，下面展示通过EditorUtility.DisplayCustomMenu()展示自定义菜单方式。
```csharp
            Vector2 mousePosition = e.mousePosition;
            //设置菜单项
            var options = new GUIContent[]{
                    new GUIContent("Test1"),
                    new GUIContent("Test2"),
                    new GUIContent(""),
                    new GUIContent("Test/Test3"),
                    new GUIContent("Test/Test4"),
                };
            //设置菜单显示区域
            var selected = -1;
            var userData = Selection.activeGameObject;
            var width = 100;
            var height = 100;
            var position = new Rect(mousePosition.x, mousePosition.y - height, width, height);
            //显示菜单
            EditorUtility.DisplayCustomMenu(position, options, selected, delegate (object data, string[] opt, int select)
            {
                Debug.Log(opt[select]+",name:"+userData.name);
            }, userData);
```
### Scene视图中禁用选择对象
```csharp
        Event e = Event.current;
        if (e != null)
        {
            int controlID = GUIUtility.GetControlID(FocusType.Passive);
            if (e.type == EventType.Layout)
            {
                HandleUtility.AddDefaultControl(controlID);
            }
        }
```
上述代码放到SceneView.duringSceneGui回调中。可以实现Scene视图无法选择对象，只能通过Hierarchy视图选择对象。通常用于复杂界面编辑，避免Scene视图误操作别的对象。
- FocusType.Passive 禁止控件接收鼠标、键盘焦点
- GUIUtility.GetControlID 获取控件ID
- EventType.Layout 布局事件
- HandleUtility.AddDefaultControl 添加默认控件ID

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

- 实质上OnSceneGUI()中绘制的内容也属于Gizmos，也会受编辑器中开关Gizmos的显示控制。
- 上文中提到的SceneView.duringSceneGui回调中其实也是可以进行类似OnSceneGUI()中的绘制的。同样2D GUI需要在Handles.BeginGUI()和Handles.EndGUI()之间进行绘制。**区别就是duringSceneGui回调中的绘制不要求选中目标物体，绘制的内容可以实现常驻SceneView**

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
绘制需要在``OnDrawGizmos``或者``OnDrawGizmosSelected``方法中进行，这两个方法都要求类继承自MonoBehaviour。
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
通过DrawGizmo标签来实现，如下建立一个TestDrawGizmo.cs的脚本，放到Editor目录下。
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
未选中物体时，也可以在SceneView界面内显示Light组件物体的名字。
当我们选中Light组件对应的物体时，SceneView界面内结果如下：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220705185822.png)


下面脚本放到Editor目录后，Scene场景中物体处于未选中状态时可以显示物体名。
```csharp
public class ShowObjName
{

    [DrawGizmo(GizmoType.NonSelected)]
    static void DrawGameObjectName(Transform transform, GizmoType gizmoType)
    {
        Handles.Label(transform.position, transform.gameObject.name);
    }
}
```
本示例中，通过GizmoType.NonSelected来控制只对未选中的物体生效。

#### 使用Gizmos进行Debug
Gizmos 提供了Drawline,DrayRay,DrawSphere等方法，可以方便开发过程中的调试和验证。


### Handles
在OnSceneGUI()方法中调用，用来丰富SceneView界面的显示。[官方示例](https://docs.unity3d.com/2021.3/Documentation/Manual/GizmosAndHandles.html)
也可以在SceneView.duringSceneGui回调中进行绘制。

## 扩展Game视图
运行模式下Game视图可以通过OnGUI()函数内绘制GUI，是一种很古老的方式了。在非运行模式下其实也可以绘制GUI，一般处理方式是在类（需要继承自MonoBehaviour）名前加上``[ExecuteInEditMode]``，表示该脚本可以在非运行状态下执行各个生命周期。必要时辅助UNITY_EDITOR宏，用以发布时剥离相关代码。
```csharp
#if UNITY_EDITOR
[ExecuteInEditMode]
public class Test : MonoBehaviour
{
    // 其中值得介绍的是OnEnable和OnDisable
    // 因为改了代码之后会将数据清零，而且不会执行Awake和Start
    // 但是会在编译前执行OnDisable,编译后会执行OnEnable
    // 可以用这一个时机，对一些委托的绑定与解绑，或者数据的初始化和销毁
    // 当然正常的SetActive也会触发这两个时机
    void OnEnable()
    {
    }

    void OnDisable()
    {
    }

     void OnGUI()
    {
        if(GUILayout.Button("Click"))
        {
            Debug.Log("Hello World!");
        }
    }
}
#endif
```

## 一些实用Attribute
- [InitializeOnLoadMethod]
在Class内部修饰静态方法，起到自动调用目标方法的功能。
- [InitializeOnLoad]
修饰Class，和类的静态构造函数结合使用，可以达到和[InitializeOnLoadMethod]一样的目的。
- [DidReloadScripts]
修饰静态方法；代码被编译好后，会被执行
```csharp
	[DidReloadScripts]
	private static void Reload()
	{
	}
```

## 其它重要委托
- 编辑器Update委托 EditorApplication.update+=

## 编辑器扩展涉及到的类
### GUI,GUILayout；EditorGUI,EditorGUILayout
#### GUI和GUILayout
这二者既可以用于运行时在OnGUI()函数中显示UI，也可以用于一些扩展的编辑器面板（Inspector重写、扩展的Window等等）。
- GUI
一种需要通过自行计算Rect（用于设置坐标和组件的尺寸）来显示UI组件的类
- GUILayout
是基于GUI的实现，自动进行排版，计算坐标和宽高
#### EditorGUI和EditorGUILayout
这二者只能用于扩展的编辑器面板显示UI。
- EditorGUI
需要通过自行计算Rect来显示UI
- EditorGUILayout
基于EditorGUI的实现，会自动进行排版，计算坐标和宽高

### Handles，Gizmos
上文中已涉及，不赘述

### EditorUtility,EditorGUIUtility,AssetDatabase,Selection
Unity内置的一些工具类，涉及到序列化对象修改、报错；资源读取、保存等
- EditorUtility： Unity内置的关于Editor使用的工具类，常用API有：
1,资源置脏
```csharp
EditorUtility.SetDirty(Object obj);//标记目标资源为“脏”
AssetDatabase.SaveAssets();//保存项目内的“脏数据”
```
2,显示对话框
``EditorUtility.DisplayDialog``
3,进度条
``EditorUtility.DisplayProgressBar，EditorUtility.ClearProgressBar``
- EditorGUIUtility
1,搜索框
EditorGUIUtility.ShowObjectPicker
2,选中提示
EditorGUIUtility.PingObject

- AssetDatabase: 在编辑器模式下，对项目资源的管理（创建资产、获得资产路径、资产加载、资产刷新、资产保存）
- Selection: 含有当前选择的资源文件、文件夹或场景中选中的目标对象信息 
下面代码展示了获得选中文件夹下的所有Texture(需要先选中一个目标文件夹)
```csharp
var texs = Selection.GetFiltered<Texture>(SelectionMode.DeepAssets); //进行深度遍历所有文件夹
```
下面代码展示：创建一个Cube并选中它
```csharp
if (GUILayout.Button("创建一个 Cube，同时选中"))
{
    var obj = GameObject.CreatePrimitive(PrimitiveType.Cube);  
    Selection.activeGameObject = obj; 
}
```

## 常用编辑器UI绘制API
### 空行
- EditorGUILayout.Separator() 一个比较大的空行
- EditorGUILayout.Space() 一个比较小的空行
- GUILayout.Space(10f) 可控具体空多少行
- GUILayout.FlexibleSpace() 底层自动计算空白区

### 布局
#### 水平，垂直布局
- GUILayout.BeginHorizontal()和GUILayout.EndHorizontal() 二者一起可以让内部GUI水平排列。同理还有Vertical。
#### 折叠区域
- 折叠组 EditorGUILayout.BeginFoldoutHeaderGroup()和EditorGUILayout.EndFoldoutHeaderGroup()
- 折叠 EditorGUILayout.Foldout()

#### 开关控件
- 单个开关 EditorGUILayout.Toggle，EditorGUILayout.ToggleLeft
- 开关组 EditorGUILayout.BeginToggleGroup，EditorGUILayout.EndToggleGroup

#### 滚动区域
- EditorGUILayout.BeginScrollView()和EditorGUILayout.EndScrollView()

### 类型字段
EditorGUILayout.LabelField() 标签字段 
EditorGUILayout.IntField() 整数字段 
EditorGUILayout.FloatField() 浮点数字段 
EditorGUILayout.TextField() 文本字段 
EditorGUILayout.Vector2Field() 二维向量字段 
EditorGUILayout.Vector3Field() 三维向量字段 
EditorGUILayout.Vector4Field() 四维向量字段 
EditorGUILayout.ColorField() 颜色字段
EditorGUILayout.CurveField() 曲线字段（AnimationCurve）

### 滑动条、进度条
EditorGUILayout.Slider()，EditorGUILayout.IntSlider()
EditorGUILayout.MinMaxSlider() 双滑块滑动条
EditorGUI.ProgressBar() 

### 提示框
EditorGUILayout.HelpBox()

### 枚举选择
- EditorGUILayout.EnumPopup 单选枚举
- EditorGUILayout.EnumFlagsField 多选枚举
- EditorGUILayout.IntPopup和EditorGUILayout.MaskField
EditorGUILayout.IntPopup 单选整型
EditorGUILayout.MaskField 多选整形

### 一些注意项
- EditorGUILayout.DropdownButton 并不是下拉按钮，而是鼠标按下就会触发的按钮。而Button是鼠标放开才会触发。


## 继承关系
``ScriptableWizard:EditorWindow:ScriptableObject:Object``
``Editor:ScriptableObject:Object``