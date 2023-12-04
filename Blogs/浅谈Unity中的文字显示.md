以UGUI系统为例，字体分为内置字体、外部导入字体、自定义字体。
文字一般通过``Text``组件进行显示。目前Unity已经官方支持TextMeshPro，因此也有一些文字通过``TextMeshPro-Text(UI)``组件进行展示，可以支持比Text组件更强大的功能（更强大的富文本、图文混排）。同时我们也可以借用工具（比如bmfont）设计效果更复杂的所谓美术字，Text组件是默认支持美术字显示的。

## 内置字体
当创建Text组件后，默认的字体是Arial,这是个西文字体，是Unity自带的字体。之所以在编辑器和手机上发现也可显示中文，是Unity底层对其进行了处理，在运行时遇到Arial字库中没有的字，会从系统的默认字库中查找到对应的文字。因此若遇到刚好系统字库也没有某个字符，就会导致字体不显示或者乱码的问题。
DroidSansFallback字体是Android设备初期时默认的中文字体，由谷歌委托台湾华康科技设计的，是可以免费商用的字体。这个字体的观感和微软的雅黑类似，看起来非常的舒适，支持东亚的各国的文字（繁体中文、简体中文、韩文、日文）。但是由于Android系统的可定制，太过自由，不排除有些厂商会修改默认字体，因此在安卓机器中若无DroidSansFallback字体，Unity程序在遇到Arial字库中没有的字符时，也无法查找到，造成显示上的问题，[参考这里](https://blog.csdn.net/cbbbc/article/details/71242055)。

基于上述原因，不建议使用默认字体Arial，除非你的游戏是纯英文的，不考虑本地化和特殊字符。

``PS:笔者测试DroidSansFallback字体样式和Arial很相近，肉眼几乎无法区分``，可以点击[这里](https://download.csdn.net/download/iningwei/83914827)下载该字体。

## 动态字体
上述提到不建议使用内置Arial字体，因此玩家可以根据需要把.ttf字体拖到Untiy内，比如，我把C:\Windows\Fonts目录下``幼圆 常规``拖到Unity内为：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220228182208.png)

对应的Inspector界面为：
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220228182317.png)

Font Size值默认为16，数值越大设置文字对应的texture越大。
Character默认使用Dynamic选项，若使用Custom set选项，则字体中只有用户在Custom Chars内设置的字符才会显示。
Include Font Data默认是勾选的，在打包的时候会把字体添加到包中，若不勾选，则不会把该字体打包。
Font Names是字体名，有些api需要通过字体名获得字体，就要传入这里显示的名字，当然这个名字是可以修改的。注意这个名字要和XXX.ttf中的XXX区分。

### 动态字体丢失、破碎问题
1,我们用到的ttf字体，在Text每次赋值的时候Unity会生成贴图，以及相应UV信息。显示字体的时候再根据UV信息去贴图里面去取对应区域的图片信息并渲染到屏幕上。出现花屏的原因一般是贴图更新了，但是UV信息没有更新，这个时候需要用新的UV去取。
```csharp
bool isDirty = false;
    Font dirtyFont = null;
  
    void Awake()
    {
        Font.textureRebuilt += delegate(Font font1)
        {
            isDirty = true;
            dirtyFont = font1;
        };
    }
  
    void LateUpdate()
    {
        if (isDirty)
        {
            isDirty = false;
            foreach (Text text in GameObject.FindObjectsOfType<Text>())
            {
                if (text.font == dirtyFont)
                {
                    text.FontTextureChanged();
                }
            }
            dirtyFont = null;
        }
    }
```

2,上面截图中可以看到ttf字体在Unity中会生成一个texture和一个material文件。当需要显示文字的时候，会通过RequestCharactersInTexture函数向Font请求更新文字信息，然后使用GetCharacterInfo获取文字信息来渲染。在调用GetCharacterInfo的时候要保证文字都通过RequestCharactersInTexture请求过了。如果请求的时候，Font内部维护的texture不够用了，就会触发textureRebuildCallback的回调，通知外部Font的对象，其内部的texture被更新了，外部应该重新刷新。
Unity的Font默认的texture大小是256X256，在纯英文的情况下，是完全够用的。但是汉字，日文等字体就完全不够了。如果unity的刷新回调触发，则要重新刷新所有的文本控件。这样就容易出现字体破碎的情况。因为一般情况下我们请求的文字不会很多，使用的texture不会超过256x256,unity不会自动扩展texture大小。但我们请求的文字过多时，texture如果不够了，这时就需要在回调函数中刷新字体，如果我们连续请求文字，每次texture都需要扩展（需要回调刷新），于是就会出现不停刷新，字体也可能会破碎。
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class FontTextureSize : MonoBehaviour {

    // Use this for initialization
    void Start () {
        Font stx = Resources.Load<Font>("字体名");//注意这里就需要传入字体名，见上文
        Texture tex = stx.material.mainTexture;
        Debug.Log(string.Format("texture:{0} {1}", tex.width, tex.height));
        TextAsset ta = Resources.Load<TextAsset>("word");//word.txt内存储了很多文字
        string word = ta.text;
        stx.RequestCharactersInTexture(word);
        tex = stx.material.mainTexture;
        Debug.Log(string.Format("texture:{0} {1}", tex.width, tex.height));
    }
    
    // Update is called once per frame
    void Update () {
        
    }
}
```

## 自定义字体
自定义字体一般使用bmfont来制作，不再赘述